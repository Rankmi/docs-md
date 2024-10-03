
# Procedimiento de Activación

Esto actualmente solo se puede hacer por base de datos o desde rails console.
#### Paso a Paso

1. **En el Módulo Core:**
	- Ingresar al rails c desde la consola o argocd de cualquier pod del master.
	- Correr el siguiente script con la adaptación del token a la empresa correspondiente
```ruby
  enterprise_token = ''
  Apartment::Tenant.switch! Tenant.find_by(token: enterprise_token).scheme
  Enterprise.first.update(show_contract_versions: true)
```

2. **En el Módulo Payroll:**
	- Ingresar al rails c desde la consola o argocd de cualquier pod del payroll.
	- Correr el siguiente script con la adaptación del token a la empresa correspondiente
```ruby
  enterprise_token = ''
  Tenant.find_by(token: enterprise_token).update(position_module: true, syncronization: true)
```



Se puede revisar el registro de empresas activas en el siguiente [dashboard](https://app.us.luzmo.com/s/configuraciones-prendidas-master-d4856sk8uabcth5a)

# Validación de Datos en el Módulo Core

- **Datos Necesarios:**
    - Catálogo de Cargos, Áreas, Razones Sociales, Sucursales y Centros de costos.
    - Catálogo de Posiciones
    - Opcional: Usuarios con posiciones asignadas para facilitar la creación de contratos.

```ruby
# Este comando se encarga de publicar y verificar 
bundle exec rails sync:position_errors['enterprise_token']

# Este comando se encarga de publicar todos los datos 
# organization_positions, areas, positions, branches, cecos, company
bundle exec rails sync:run['enterprise_token']
```
        
    
- **Pasos para Validar:**
    1. Acceder al módulo Core.
    2. Navegar a la sección de administración -> Posiciones
    3. Verificar que las posiciones estén creadas y con la información correspondiente.
        - Datos Obligatorio para crear un contrato de forma correcta
            - Country
            - Razón social (Empresa)
            - Área (Estructura organizacional)
            - Cargo
            - Centro de costo
    4. Confirmar que la información de posiciones esté actualizada.
        

## Validación de Datos en el Módulo Payroll

- **Datos Necesarios:**
    - Los usuarios deben estar creado en el país correspondiente a la posición que se quiere asignar

- **Pasos para Validar:**
    1. Acceder al módulo Payroll.
    2. Navegar a la sección de gestión -> Contratos
    3. Crear un nuevo contrato
    4. Confirmar que la información cargue al crear el contrato de forma correcta.

### Sincronización interna

Se debe revisar que los datos entre contratos y personas sean consistentes de forma interna. Para esto se puede ejecutar el siguiente script:

```ruby
Contract.active.current_versions.filter_map { |c|
  Current.country = c.country_namespace
  p = c.person

  c.id unless c.area_id == p.area_id && c.position_id == p.position_id && c.supervisor_id == p.direct_manager_id
}
```

Para realizar la sincronización se puede correr el siguiente script:

```ruby
Contract.active.current_versions.filter_map { |c|
  Current.country = c.country_namespace
  p = c.person

  c.id unless c.area_id == p.area_id && c.position_id == p.position_id && c.supervisor_id == p.direct_manager_id
}.each { |id|
    pp id
    contract = Contract.find(id)
    Current.country = contract.country_namespace

    contract.skip_sns_publish = true
    contract.send(:sync_user_organization_data)
  }
```

### Validación de contratos

Antes de sincronizar los datos es importante revisar que los contratos se puedan guardar, para ello se puede utilizar el siguiente script.

```ruby
Contract.unscoped.active.current_versions.find_each.reject { |contract|
  Current.country = contract.country_namespace

  contract.valid?
}.map { |c| [c.id, c.errors.full_messages] }
```

Los casos problematicos se deberán resolver a mano.

### Determinar empleados con múltiples contratos

Para encontrar los empleados que tienen múltiples contratos se puede correr el siguiente script de Ruby:

```ruby
count = Employee.count
Employee.find_each.each_with_index.filter { |emp, index|
  pp (index/count.to_f * 100).round(2) if index % 150 == 0
  emp.contracts.active.current_versions.count > 1
}.map { |e| e[0] }.map { |emp| [emp.person.identifier, emp.contracts.active.current_versions.count] }
```

## Validación cruzada

Debido a la existencia de información histórica es importante revisar que los datos entre usuarios que existian de forma previa sea consistente, para ellos se puede revisar con la siguiente consulta:

```sql
-- Tokens en la base de datos payroll que no están en master:

SELECT token

FROM shared_extensions.dblink('master_db_connect',
'SELECT token FROM "268658f5-fe53-4197-aa6e-6b1a069ea911-ingevec".users')
AS t1(token varchar)
EXCEPT
SELECT token
FROM "2dff43ea-af8c-4648-bb20-6ccfd19c9db2-parks-hospitality-holdings".users;

-- Tokens en la base de datos master que no están en payroll:

SELECT token FROM "2dff43ea-af8c-4648-bb20-6ccfd19c9db2-parks-hospitality-holdings".users
EXCEPT
SELECT token FROM shared_extensions.dblink('master_db_connect',
'SELECT token FROM "268658f5-fe53-4197-aa6e-6b1a069ea911-ingevec".users')
AS t1(token varchar);
```

## Primer sincronización

Al sincronizar desde payroll se debe correr el siguiente script para garantizar que se toman los valores del país que corresponde:

```ruby
total = User.count
User.find_each.each_with_index { |user, index|
  pp "#{(index/total.to_f * 100).round(2)}%" if index % 100 == 0
  Current.country = user.country_camelize
  user.send(:sns_after_update)
}
```

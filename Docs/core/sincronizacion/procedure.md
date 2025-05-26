## Sincronizacion

### Verificar errores en Master

```bash
bundle exec rake "enterprises:activate_sync[token_de_la_empresa]"
```

### Verificar errores en Payroll

#### Verificacion de errores

```bash
bundle exec rake "sync:check_before_sync[token_de_la_empresa]"
```

#### Homologacion de contrato

```bash
bundle exec rake "sync:contract_homologate[token_de_la_empresa]"
```

### Adicionales (Ignorar si las posiciones no estan activas en la empresa)

Si las posiciones están activas, se debe correr el siguiente comando para activar las posiciones tambien en payroll:

```ruby
tenant = Tenant.find_by(token: 'token_de_la_empresa')
Apartment::Tenant.switch!(tenant.scheme)
tenant.update!(position_module: true)
```

Y tambien este en el core:

```ruby
tenant = Tenant.find_by(token: 'token_de_la_empresa')
Apartment::Tenant.switch!(tenant.scheme)
Enterprise.first.update!(show_contracts_verion: true)
```


### Activacion

#### Payroll

Para encender la sincronizacion en Payroll, se debe correr el siguiente comando:

```ruby
tenant = Tenant.find_by(token: 'token_de_la_empresa')
tenant.update!(syncronization: true)
```

### Publicación

El orden depende de donde están centralizados los (Core o Payroll), si es en el Core, entonces se debe correr en este orden:

```bash
# Primero en Core
bundle exec rake "sync:run[token_de_la_empresa]"
# Luego Payroll
bundle exec rake "sync:run_publish[token_de_la_empresa]"
```

Si los datos están centralizados en Payroll, se debe correr en este orden:

```bash
# Primero Payroll
bundle exec rake "sync:run_publish[token_de_la_empresa]"
# Luego en Core
bundle exec rake "sync:run[token_de_la_empresa]"
```

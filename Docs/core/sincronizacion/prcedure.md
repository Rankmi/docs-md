## Sincronizacion

### Verificar errores en Master

```bash
$ bundle exec rake "enterprises:activate_sync[token_de_la_empresa]"
```

### Verificar errores en Payroll

#### Verificacion de errores

```bash
$ bundle exec rake "sync:check_before_sync[token_de_la_empresa]"
```

#### Homologacion de contrato

```bash
$ bundle exec rake "sync:contract_homologate[token_de_la_empresa]"
```

### Adicional para payroll

Si las posiciones están activas, se debe correr el siguiente comando para activar las posiciones tambien en payroll:

```ruby
tenant = Tenant.find_by(token: 'token_de_la_empresa')
Apartment::Tenant.switch!(tenant.scheme)
tenant.update!(position_module: true)
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

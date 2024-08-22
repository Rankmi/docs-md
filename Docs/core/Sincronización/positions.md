
Para activar la sincronización se debe realizar mediante consola de rails

```ruby
# Activar sincro
enterprise_token = 'd42f09ed-09ee-4302-9503-40f547e68a12'
Apartment::Tenant.switch! Tenant.find_by(token: enterprise_token).scheme
Enterprise.first.update(show_contract_versions: true)
```

Se puede revisar el registro de empresas activas en el siguiente [dasboarh](https://app.us.luzmo.com/s/configuraciones-prendidas-master-d4856sk8uabcth5a)


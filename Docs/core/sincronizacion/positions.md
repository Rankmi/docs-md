
Para activar la sincronización se debe realizar mediante consola de rails

```ruby
# Activar sincro
enterprise_token = 'd42f09ed-09ee-4302-9503-40f547e68a12'
Apartment::Tenant.switch! Tenant.find_by(token: enterprise_token).scheme
Enterprise.first.update(show_contract_versions: true)
```

Se puede revisar el registro de empresas activas en el siguiente [dashboard](https://app.us.luzmo.com/s/configuraciones-prendidas-master-d4856sk8uabcth5a)




Verificar diferencias de token entre master y payroll

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
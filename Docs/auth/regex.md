Para configurar la seguridad de la contraseña en una empresa se debe correr el siguiente script sobre el repositirio de [Links-api](https://github.com/rankmi/links-api) sobre la empresa correspondiente y con el regex final que se desea.

```ruby
# Cantidad de digitos minimo para la contraseña
password_validator_minlen = 8
# Regex para validar la contraseña 
password_validator_regexp = '^(?=.*[0-9])(?=.*[!#$%&()*+,./:;<=>?@^_`{|}~])(?=.*[A-Z])(?=.*[a-z])\S{8,12}$'

t = Tenant.find_by(token: '834efe49-cf5f-4728-81f9-c437bc70c0d1')
Apartment::Tenant.switch! t.scheme

LoginSetup.all.each do |l|
  l.update(password_validator_minlen: password_validator_minlen, password_validator_regexp: password_validator_regexp)
end
```

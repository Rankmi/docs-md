
Actualmente, el módulo de personas soporta segmentos, lo que permite a los usuarios segmentar colaboradores según una condición o filtro. Estos segmentos se almacenan en la tabla segments y funcionan de la siguiente manera:

## Descripción de la tabla

- Campo `user_tokens`: Contiene todos los tokens de los usuarios que están actualmente en el segmento. Este campo se actualiza en segundo plano cada vez que una de las condiciones cambia, según un usuario cumpla o no con dicha condición, dentro del archivo [update_records.rb](https://github.com/Rankmi/master-api/blob/main/app/services/segments/update_records.rb) en la línea 30. Este campo no es publicado por SNS debido a que, en algunos casos, su longitud puede ser muy grande y sobrepasar los límites de tamaño de un mensaje SNS.

- Campo `added_tokens`: Este campo contiene los tokens de todos los usuarios añadidos en la última actualización del segmento. Inicialmente, el campo se crea vacío, y luego de que el segmento sufra un cambio, donde se añadan más usuarios, este campo se llenará con el último listado de tokens insertados en el segmento. Este campo sirve para publicar los tokens añadidos al segmento por SNS, lo cual se realiza en el archivo [publication.rb](https://github.com/Rankmi/master-api/blob/main/app/services/segments/publication.rb) en la línea 34, haciéndolo por lotes de 2000 elementos en caso de que los tokens sean muchos.

- Campo `removed_tokens`: Este campo funciona de manera similar a added_tokens, pero en sentido contrario, ya que se llena con los tokens de los usuarios que fueron extraídos del segmento. Sirve para su posterior publicación, la cual se realiza en el archivo [publication.rb](https://github.com/Rankmi/master-api/blob/main/app/services/segments/publication.rb) en la línea 40, enviando un listado de tokens removidos también por lotes de 2000 elementos.

## Triggers (Disparadores)

Los segmentos, a nivel de código, contienen disparadores que permiten que un segmento se auto-actualice si un usuario, área, cargo, posición, etc., sufre una transformación.

Por ejemplo, si un segmento contiene 5 usuarios y está establecido un filtro de Área es Departamento de Desarrollo, y en algún momento uno de esos usuarios es cambiado al área de TI, se disparará un trigger para actualizar el segmento, el cual quedará solo con 4 usuarios.

Estos disparadores se almacenan en el campo updating_fields, el cual guarda un indicador de qué filtros tiene, usando un formato de arrays similar al siguiente:

```sql
{active,position_name,area_name,name,rut}
```

El disparador se invoca según sea el caso desde el modelo correspondiente (User, Area, Position, etc), de la siguiente manera:

```ruby
after_commit :update_segments

def update_segments
  Segment.updating_any_of(['campo_disparador']).find_each do |segment|
    Segments::UpdateRecords.perform_in(15.seconds, segment.id, segment.token)
  end
end
```

Esto hace se filtren solos los segmentos que contengan el disparador indicado usando el scope `updating_any_of` y se ejecute un job en los próximos 15 segundos (si hay disponibilidad) que actualiza el segmento ejecutando nuevamente los filtros y revisando si hay cambios en la cantidad de usuarios y tokens actualmente registrados, manteniendo de esta forma los segmentos actualizados.

Dejo un ejmplo real del disparador en el modelo [Area](https://github.com/Rankmi/master-api/blob/main/app/models/area.rb)


```ruby
def update_segments
  return unless saved_changes.key?('name')

  Segment.updating_any_of(['area_name']).find_each do |segment|
    Segments::UpdateRecords.perform_in(15.seconds, segment.id, segment.token)
  end
end
```
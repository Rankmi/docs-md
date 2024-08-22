
Query para encontrar duplicados
```sql
select id, user_token, organization_position_token, active from "ineamflogistic-d74fc1ac-5b44-42cb-b59d-4658b7590894".organization_seats where  
active is true and user_token in (  
select user_token from "ineamflogistic-d74fc1ac-5b44-42cb-b59d-4658b7590894".organization_seats  
         where active is true  
         group by user_token  
having count(user_token) > 1) order by user_token, created_at desc;
```

Query para dejar ultimo asiento activo
```sql
WITH OldestRecords AS (  
    SELECT  
        id,  
        user_token,  
        ROW_NUMBER() OVER (PARTITION BY user_token ORDER BY created_at) AS row_num  
    FROM "ineamflogistic-d74fc1ac-5b44-42cb-b59d-4658b7590894".organization_seats  
    WHERE active = true  
      AND user_token IN (  
        SELECT user_token  
        FROM "ineamflogistic-d74fc1ac-5b44-42cb-b59d-4658b7590894".organization_seats  
        WHERE active = true  
        GROUP BY user_token  
        HAVING count(user_token) = 2  
    )  
)  
-- Luego, actualiza esos registros para desactivarlos  
UPDATE "ineamflogistic-d74fc1ac-5b44-42cb-b59d-4658b7590894".organization_seats  
SET active = false  
WHERE id IN (  
    SELECT id  
    FROM OldestRecords  
    WHERE row_num = 1  
);
```

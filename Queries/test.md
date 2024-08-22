```sql
select p.email, c.base_salary, c.type  from "02a39547-9493-478f-820d-9ee3f123b6b1-rankmi-interno".contracts c  
         inner join "02a39547-9493-478f-820d-9ee3f123b6b1-rankmi-interno".employees e on c.employee_id = e.id  
         inner join "02a39547-9493-478f-820d-9ee3f123b6b1-rankmi-interno".people p on e.person_id = p.id  
where c.current_version is true;
```

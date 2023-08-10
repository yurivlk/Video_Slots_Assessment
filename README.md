# Video_Slots_SQL_Assessment

Questions

1 -	Do we currently have any customers who are inactive? If yes, please send me their name, surname, email, address, city, and country.
```
SELECT 
    tc.first_name,
    tc.last_name,
    tc.email,
    ta.address,
    tci.city,
    coun.country
FROM
    customer AS tc
        JOIN
    address AS ta ON tc.address_id = ta.address_id
        JOIN
    city AS tci ON tci.city_id = ta.city_id
        JOIN
    country AS coun ON coun.country_id = tci.country_id
WHERE
    active = 0;
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/Question%201.png?raw=true)


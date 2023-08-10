# Video_Slots_SQL_Assessment

Questions

1 -	Do we currently have any customers who are inactive? If yes, please send me their name, surname, email, address, city, and country.
```
SELECT 
    t1.first_name,
    t1.last_name,
    t1.email,
    t2.address,
    t3.city,
    t4.country
FROM
    customer AS t1
        JOIN
    address AS t2 ON t2.address_id = t1.address_id
        JOIN
    city AS t3 ON t3.city_id = t2.city_id
        JOIN
    country AS t4 ON t4.country_id = t3.country_id
WHERE
    active = 0;
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/Question%201.png?raw=true)

2 - Please send me a list of actors (name and surname) who took part in a “Horror” movie.
```
SELECT 
    first_name,
    last_name
FROM 
	actor
WHERE actor_id IN
(SELECT
	DISTINCT(t1.actor_id)
FROM 
	actor AS t1
		JOIN
	film_actor as t2 ON t2.actor_id = t1.actor_id
		JOIN
	film as t3 ON t3.film_id = t2.film_id
		JOIN
	film_category as t4 ON t4.film_id = t3.film_id
		JOIN
	category as t5 ON t5.category_id = t4.category_id
WHERE
	t5.name ='Horror')
ORDER BY
	first_name;
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/Question%201.png?raw=true)

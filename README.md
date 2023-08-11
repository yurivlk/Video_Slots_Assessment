# Video_Slots_SQL_Assessment

Questions

### 1 -	Do we currently have any customers who are inactive? If yes, please send me their name, surname, email, address, city, and country.
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

### 2 - Please send me a list of actors (name and surname) who took part in a “Horror” movie.
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
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/images/Question%202.png?raw=true)

### 3 - Can you please tell me how many of those customers (1 amount) have rented movies from Mile Hilyer and who has spent less than 60 euros in DVD rentals?

```
SELECT
	COUNT(DISTINCT(t1.customer_id)) as Qnty_of_pls_1amount
FROM
	payment as t1
	JOIN 
rental as t2 ON t2.rental_id = t1.rental_id
	JOIN 
staff as t7 ON t7.staff_id = t1.staff_id
	JOIN
inventory as t3 ON t3.inventory_id = t2.inventory_id
WHERE 
	t1.amount <= 1 AND t7.first_name = 'Mike' AND t7.last_name = 'Hillyer';
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/images/Question%203.png?raw=true)

```
CREATE TEMPORARY TABLE temp AS(
SELECT 
	customer_id,
    SUM(amount) as total_spent
FROM
	payment 
WHERE customer_id IN
(SELECT
	DISTINCT(t1.customer_id)
FROM
	payment as t1
	JOIN 
rental as t2 ON t2.rental_id = t1.rental_id
	JOIN 
staff as t7 ON t7.staff_id = t1.staff_id
	JOIN
inventory as t3 ON t3.inventory_id = t2.inventory_id
WHERE 
	t1.amount <= 1 AND t7.first_name = 'Mike' AND t7.last_name ='Hillyer')
GROUP BY customer_id
HAVING total_spent < 60);

SELECT 
	temp.customer_id,
    temp.total_spent,
    cust.first_name,
    cust.last_name,
    cust.email
FROM
	temp
JOIN customer as cust ON cust.customer_id = temp.customer_id;
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/images/Question%203b.png?raw=true)

### 4 - From next month, I want to increase the rental rates of all movies to 1€. Please send an UPDATE script so I can run it when the time comes.
```
UPDATE film 
SET rental_rate = rental_rate + 1;
```



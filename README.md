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

### 5 We got a new stock of drama movies in Spanish. Please send me an INSERT script to add the Spanish Language and prepare an UPDATE script to change the original language of all the drama movies in Spanish, to English.
```
INSERT INTO language (name)
VALUES ('Spanish');

UPDATE film
SET original_language_id = (
    SELECT language_id
    FROM language
    WHERE name = 'English'
)
WHERE language_id = (
    SELECT language_id
    FROM language
    WHERE name = 'Spanish'
) AND film_id IN (
    SELECT film_id 
    FROM film_category
    WHERE category_id = 7
);
```

### 6 - Some customers took more time than the rental duration of the movie to return it back to us and I want to warn them via an email. Please send me their name, surname, and email address.

```
CREATE TEMPORARY TABLE temp1 AS(
SELECT 
	t4.customer_id,
	t4.first_name,
    t4.last_name,
    t4.email,
    t3.rental_duration,
    DATEDIFF(t1.return_date, t1.rental_date) as diff 
FROM rental as t1
	JOIN 
inventory as t2 ON t2.inventory_id = t1.inventory_id
	JOIN 
film as t3 ON t3.film_id = t2.film_id
	JOIN 
customer as t4 ON t4.customer_id = t1.customer_id);


SELECT 
	DISTINCT(customer_id),
	first_name,
	last_name,
    email,
	CASE WHEN diff > rental_duration THEN 'Delayed'
    ELSE 'on_time'
	END as result
FROM 
	temp1
HAVING result = 'Delayed';
```
![Texto Alternativo](https://github.com/yurivlk/Video_Slots_SQL_Assessment/blob/main/images/Question%206.png?raw=true)

### 7 - I would like to remove all the information of the least 3 rented movies from the database to prevent customers from renting them again. Please send me the DELETE script so I can run it on the live server.
```
SELECT 
    COUNT(rental_id) AS rent_count, 
    film_id
FROM
    rental AS t1
        JOIN
    inventory AS t2 ON t1.inventory_id = t2.inventory_id
GROUP BY film_id
ORDER BY rent_count ASC;

DELETE FROM rental 
WHERE inventory_id IN (1839,1840,2661,2662,4161,4162);

DELETE FROM inventory 
WHERE inventory_id IN (1839,1840,2661,2662,4161,4162);

DELETE FROM film_category
WHERE film_id IN (400, 584, 904);

DELETE FROM film_actor
WHERE film_id IN (400, 584, 904);

DELETE FROM film
WHERE film_id IN (400, 584, 904);
```

# Task 2

### Task 2:
A day after, you received a Technical Issue from Customer Support saying:
“Hi, user id 123456 won SEK500 on the Feb 25 and during the previous day, we blocked this
user and still got the money. Also, user id 123457 received half of the winnings and did not get
notified. Can you please check the reason behind these issues?”
After you checked the code, you found a script which is found on the next page. Please write
down brief descriptions of any bugs that you have identified (inline comments are also
acceptable), suggesting solutions for the code (clearly marking changes).

```
$result = $this->payTransaction(555555, 'transactions') // Not setting the notify variable as False, in order to notify the user;
if(!$result){
die('No money to the user');
}
exit;

function payTransaction($tid, $table, $notify = true){
$casino = new Casino();
$trans = $this->getTransaction($tid, $table);
$user = User::getUser($trans['user_id']);


if($user->isBlocked()){
return false; // Stop processing for blocked users
} else{
$trans_amount = $trans['amount']
}
if($trans_amount > 0){
$result = $casino->changeUserBalance($user, $trans_amount);
}else
return false;

if($casino->okResult($result)){
DB::delete($table, ['id' => $tid], $user->getId());
if($notify){
$this->notifyUserTransaction($trans, $user, $trans_amount, true);
}
return $result;
}
}
```


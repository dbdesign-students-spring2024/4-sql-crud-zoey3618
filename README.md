# SQL CRUD
## Restaurant Finder
* practice data
id
name (name of restaurants)
category (e.g. Chinese)
price_tier (Cheap, Medium, and Expensive)
neighborhood (6 neighborhoods in NYC)
opening_hours(in 24 hour format)
closing_hours(in 24 hour format)
average_rating (integer from 0 to 5)
good_for_kids (True or False)


* sql code 
```sh
sqlite3 restaurants.db
```
create table
```sh
CREATE TABLE restaurants (
    id INT PRIMARY KEY,
    category TEXT,
    name TEXT NOT NULL,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hour TIME,
    closing_hour TIME,
    average_rating INT,
    good_for_kids BOOLEAN
);
```
a table for reviews
```sh
CREATE TABLE reviews (
    review_id INTEGER PRIMARY KEY,
    restaurant_id INTEGER,
    review_text TEXT,
    rating INTEGER,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants(id)
);
```
import restaurant.csv to table restaurants
```sh
.mode csv
.import /Users/moxi/Desktop/database/4-sql-crud-zoey3618/data/restaurant.csv restaurants
```
### Queries
* 1.Find all cheap restaurants in a Bronx
```sh
SELECT * FROM restaurants WHERE price_tier = 'cheap' AND neighborhood = 'Bronx';
```
Here is part of the result:
| id | category | name             | price_tier | neighborhood | opening_hour | closing_hour| average_rating | good_for_kids |
|----|---------|------------------|------------|--------------|--------------|--------------|--------|----------|
| 6  | Greek   | Una Pizza        | cheap      | Bronx        | 8:00         | 21:29        | 1      | false    |
| 8  | Indian  | Saigon Shack     | cheap      | Bronx        | 10:26        | 23:58        | 3      | true     |
| 9  | Greek   | the Gramercy Room| cheap      | Bronx        | 9:52         | 23:45        | 3      | false    |
| 12 | Italian | Up Thai          | cheap      | Bronx        | 9:44         | 23:41        | 1      | true     |
| 30 | Greek   | Sky Pavilion     | cheap      | Bronx        | 9:40         | 18:33        | 5      | true     |
| 49 | Greek   | TSUMO            | cheap      | Bronx        | 8:28         | 21:00        | 2      | false    |

* 2.Find all restaurants in Indian with 3 stars or more, ordered by the number of stars in descending order.
```sh
SELECT * FROM restaurants WHERE category = 'Indian' AND average_rating >= 3 ORDER BY average_rating DESC;
```
Here is the part of result:
| id  | category | name           | price_tier | neighborhood   | opening_hour | closing_hour | average_rating | good_for_kids |
|-----|----------|----------------|------------|----------------|--------------|--------------|----------------|---------------|
| 15  | Indian   | Sky Pavilion   | expensive  | Brooklyn       | 8:05         | 21:19        | 5              | true          |
| 23  | Indian   | Una Pizza      | expensive  | Harlem         | 8:34         | 23:15        | 5              | true          |
| 83  | Indian   | Sky Pavilion   | expensive  | Harlem         | 8:43         | 21:45        | 5              | true          |
| 93  | Indian   | ChoCho Hot Pot | cheap      | Staten Island  | 8:46         | 21:10        | 5              | false         |
| 119 | Indian   | Le Gratin      | expensive  | Queens         | 8:18         | 21:22        | 5              | false         |
| 179 | Indian   | Saigon Shack   | medium     | Brooklyn       | 8:38         | 23:48        | 5              | false         |

* 3. Find all restaurants that are open now(17:42 right now)
```sh
SELECT * FROM restaurants WHERE strftime('%H:%M', 'now', 'localtime') BETWEEN opening_hour AND closing_hour;
```
Here is part of the result:

| id  | category | name                | price_tier | neighborhood   | opening_hour | closing_hour | average_rating | good_for_kids |
|-----|----------|---------------------|------------|----------------|--------------|--------------|----------------|---------------|
| 8   | Indian   | Saigon Shack        | cheap      | Bronx          | 10:26        | 23:58        | 3              | true          |
| 10  | Mexican  | Una Pizza           | cheap      | Staten Island  | 10:10        | 19:55        | 5              | true          |
| 21  | Greek    | ChoCho Hot Pot      | cheap      | Manhattan      | 10:12        | 20:07        | 5              | true          |
| 27  | Mexican  | Brasserie VietNam   | cheap      | Harlem         | 10:13        | 23:11        | 4              | false         |
| 29  | Mexican  | the Gramercy Room   | expensive  | Brooklyn       | 10:04        | 22:07        | 1              | false         |

* 4.Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).
```sh
INSERT INTO reviews (restaurant_id, review_text, rating) VALUES ((SELECT id FROM restaurants WHERE name = 'Saigon Shack'), 'YummyYummy~', 5);
```
```sh
INSERT INTO reviews (restaurant_id, review_text, rating) VALUES ((SELECT id FROM restaurants WHERE name = 'Up Thai'), 'Fine', 4);
```
* 5. Delete all restaurants that are not good for kids.
```sh
DELETE FROM restaurants WHERE good_for_kids = 0;
```

* 6.Find the number of restaurants in each NYC neighborhood.
```sh
SELECT neighborhood, COUNT(*) AS number_of_restaurants
FROM restaurants
GROUP BY neighborhood;
```
result:

| neighborhood   | number of counts |
|----------------|------------------|
| Bronx          | 183              |
| Brooklyn       | 179              |
| Harlem         | 158              |
| Manhattan      | 163              |
| Queens         | 145              |
| Staten Island  | 172              |




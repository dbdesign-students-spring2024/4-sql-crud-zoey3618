# SQL CRUD
## Restaurant Finder
* [practice data](`/Users/moxi/Desktop/database/4-sql-crud-zoey3618/data/restaurant.csv`)

id;
name (name of restaurants);
category (e.g. Chinese);
price_tier (Cheap, Medium, and Expensive);
neighborhood (6 neighborhoods in NYC);
opening_hours(in 24 hour format);
closing_hours(in 24 hour format);
average_rating (integer from 0 to 5);
good_for_kids (True or False);


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

* 4. Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).
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

* 6. Find the number of restaurants in each NYC neighborhood.
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


### Part2
[post.csv](/Users/moxi/Desktop/database/4-sql-crud-zoey3618/data/post.csv);
[user.csv](/Users/moxi/Desktop/database/4-sql-crud-zoey3618/data/user.csv)

* 1. create sql tables
```sh
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    password VARCHAR(255) NOT NULL,
    handle VARCHAR(255) NOT NULL
);
```

```sh
CREATE TABLE posts (
    post_id INT PRIMARY KEY,
    user_id INT NOT NULL,
    post_type VARCHAR(50) NOT NULL,
    content TEXT NOT NULL,
    recipient_id INT,
    visibility INT NOT NULL,
    post_time DATE NOT NULL
);
```
* 2. After inserting, queries part
1. Register a new User.
```sh
INSERT INTO users (user_id, email, password, handle)
VALUES ('1001', 'newuser@example.com', 'password123', 'newUserHandle');
```

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```sh
INSERT INTO posts (post_id, user_id, post_type, content, recipient_id, visibility, post_time)
VALUES (1001, 2, 'message', 'Your message content here', 279, 1, CURRENT_TIMESTAMP);
```

3. Create a new Story by a particular User (pick any User for example).
```sh
INSERT INTO posts (post_id, user_id, post_type, content, recipient_id, visibility, post_time)
VALUES (1002, 117, 'story', 'Your story content here', NULL, 1, CURRENT_TIMESTAMP);
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.
```sh
SELECT * FROM posts
WHERE visibility = 1
ORDER BY post_time DESC
LIMIT 10;
```
table:

| post_id | user_id | post_type | content                                                                     | recipient_id | visibility | post_time           |
|---------|---------|-----------|-----------------------------------------------------------------------------|--------------|------------|---------------------|
| 1002    | 117     | story     | "Your story content here"                                                   |              | 1          | "2024-02-29 16:20:59"|
| 1001    | 2       | message   | "Your message content here"                                                 | 279          | 1          | "2024-02-29 16:20:23"|
| 1       | 117     | story     | "consectetur adipiscing elit."                                              | 456          | 1          | 2/29/2024           |
| 2       | 279     | story     | "Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."        | 789          | 1          | 2/29/2024           |
| 4       | 293     | message   | "Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."        | 789          | 1          | 2/29/2024           |
| 8       | 647     | story     | "consectetur adipiscing elit."                                              | NULL         | 1          | 2/29/2024           |
| 19      | 459     | story     | "quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."| 123      | 1          | 2/29/2024           |
| 20      | 235     | message   | "Lorem ipsum dolor sit amet"                                                | NULL         | 1          | 2/29/2024           |
| 21      | 94      | story     | "quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."| NULL      | 1          | 2/29/2024           |
| 25      | 88      | message   | "consectetur adipiscing elit."                                              | 456          | 1          | 2/29/2024           |


5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```sh
SELECT * FROM posts
WHERE visibility = 1 AND post_type = 'message'
AND user_id = 117 AND recipient_id = 279
ORDER BY post_time DESC
LIMIT 10;
```

6. Make all Stories that are more than 24 hours old invisible.
```sh
UPDATE posts
SET visibility = 0
WHERE post_type = 'story' AND post_time < datetime('now', '-1 day');
```

7. Show all invisible Messages and Stories, in order of recency.
```sh
SELECT * FROM posts
WHERE visibility = 0
ORDER BY post_time DESC;
```
part of table:
| post_id | user_id | post_type | content                                                                     | recipient_id | visibility | post_time  |
|---------|---------|-----------|-----------------------------------------------------------------------------|--------------|------------|------------|
| 373     | 132     | story     | "Lorem ipsum dolor sit amet"                                                | 123          | 0          | 2/29/2024  |
| 374     | 985     | message   | "Lorem ipsum dolor sit amet"                                                | 456          | 0          | 2/29/2024  |
| 376     | 75      | story     | "consectetur adipiscing elit."                                              | 789          | 0          | 2/29/2024  |
| 377     | 380     | story     | "Ut enim ad minim veniam"                                                   | NULL         | 0          | 2/29/2024  |
| 378     | 454     | message   | "Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."        | 456          | 0          | 2/29/2024  |



8. Show the number of posts by each User.
```sh
SELECT user_id, COUNT(*) AS total_posts
FROM posts
GROUP BY user_id;
```


9. Show the post text and email address of all posts and the User who made them within the last 24 hours.
```sh
SELECT p.content, u.email
FROM posts p
JOIN users u ON p.user_id = u.user_id
WHERE p.post_time > datetime('now', '-1 day');

```
Shown:
```sh
content,email
"Your message content here",55qf932Nit@example.com
"Your story content here",6aJouj3Eky@example.com
```

10. Show the email addresses of all Users who have not posted anything yet.
```sh
SELECT u.email
FROM users u
LEFT JOIN posts p ON u.user_id = p.user_id
WHERE p.post_id IS NULL;
```




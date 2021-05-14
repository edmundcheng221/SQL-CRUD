# SQL CRUD

## Part 1: Restaurant Finder

[Practice CSV Data](./data/restaurants.csv)

Write the SQL command to create the table with the structure you determine is necessary.
```sql
sqlite> PRAGMA foreign_keys = ON;
sqlite> .schema
CREATE TABLE restaurants (
id PRIMARY KEY NOT NULL,
name TEXT NOT NULL,
category TEXT NOT NULL,
priceTier TEXT NOT NULL,
neighborhood TEXT NOT NULL,
open DATETIME,
close DATETIME,
avgRating NUMERIC NOT NULL,
goodForKids NUMERIC NOT NULL
);

CREATE TABLE reviews (
idReview INTEGER PRIMARY KEY,
restaurantName TEXT NOT NULL,
message TEXT NOT NULL,
rating NUMERIC NOT NULL
);
```

Write the SQLite commands necessary to import the data.
```sql

sqlite> .mode csv
sqlite> .headers on
sqlite> .import ./restaurants.csv restaurants
sqlite> SELECT * FROM restaurants;
```

Write a single SQL query to perform each of the following tasks:

1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```sql
sqlite> SELECT * FROM restaurants WHERE priceTier = "Cheap" and neighborhood = "Chelsea";
```

2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.

```SQL
sqlite> SELECT * FROM restaurants WHERE category = "Korean" and avgRating >= 3 ORDER BY avgRating DESC;
```
3. Find all restaurants that are open now (see hint below).

```sql
sqlite> SELECT * FROM restaurants WHERE strftime('%H', 'now') BETWEEN strftime('%H', open) AND strftime('%H', close);
```

4. Leave a review for a restaurant (pick any restaurant as an example).

```sql
INSERT INTO reviews (restaurantName,message,rating) VALUES ("Dach and Sons", "Food was very affordable",5);
```


5. Delete all restaurants that are not good for kids.

``` sql
sqlite> DELETE FROM restaurants WHERE goodForKids == "0";
```

6. Find the number of restaurants in each NYC neighborhood.

```sql
sqlite> SELECT neighborhood, COUNT(name) FROM restaurants GROUP BY neighborhood;
```

## Part 2: Social Media Application
[Practice CSV Data for Users](./data/users.csv)

[Practice CSV Data for Posts](./data/posts.csv)

Create database
```sql
(base) Edmunds-MacBook-Pro:data edmundcheng$ sqlite3 socialMediaApp.db
```

Create tables and import data
```sql
CREATE TABLE users (
id TEXT PRIMARY KEY NOT NULL,
email TEXT NOT NULL,
password TEXT NOT NULL,
handle TEXT NOT NULL
);

CREATE TABLE posts (
id INTEGER PRIMARY KEY,
sender TEXT NOT NULL,
postType TEXT NOT NULL,
receiver TEXT NOT NULL,
datePosted INTEGER,
visible TEXT NOT NULL,
content TEXT NOT NULL
);

sqlite> .mode csv
sqlite> .headers on
sqlite> .import users.csv users
sqlite> .import posts.csv posts
```

Write a single SQL query to perform each of the followin tasks:

1. Register a new User.
```sql
INSERT INTO users (id,email,password,handle) VALUES ("1001","ec3219@nyu.edu","SomePassword","edmund5555");
```

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).

```sql
sqlite> INSERT INTO posts (sender,postType,receiver,datePosted,visible,content) VALUES ("jrooksby7y","message","mschlagmanah",strftime('%s','now'),TRUE,"Hello World");
```

3. Create a new Story by a particular User (pick any User for example).

```sql
sqlite> INSERT INTO posts (sender,postType,receiver,datePosted,visible,content) VALUES ("jrooksby7y","story","NA",strftime('%s','now'),TRUE,"Hello World");
```

4. Show the 10 most recent visible Messages and Stories, in order of recency. (Doesn't matter if messages or stories)


```sql
sqlite> SELECT content FROM posts WHERE visible = "TRUE" ORDER BY datePosted DESC LIMIT 10;
```


5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.

```sql
sqlite> SELECT content FROM posts WHERE sender = "cniese6y" AND receiver = "vcarillo6y" AND visible = "TRUE" ORDER BY datePosted DESC LIMIT 10;
```

6. Make all Stories that are more than 24 hours old invisible.

```sql
UPDATE posts SET visible = "FALSE" WHERE postType = "story" AND visible = "TRUE" and strftime('%s','now') - datePosted >= 86400;
```

7. Show all invisible Messages and Stories, in order of recency. (Doesn't matter if messages or stories)

```sql
SELECT content FROM posts WHERE visible="FALSE" ORDER BY datePosted DESC; 
```

8. Show the number of posts by each User.

```sql
sqlite> SELECT sender, COUNT(content) FROM posts GROUP BY sender;
```

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.

```sql
sqlite> SELECT users.email, posts.content, users.handle AS user FROM users INNER JOIN posts ON users.handle = posts.sender WHERE strftime('%s','now') - posts.datePosted < 86400;
```

10. Show the email addresses of all Users who have not posted anything yet.

```sql
sqlite> SELECT users.email FROM users LEFT JOIN posts ON users.handle = posts.sender WHERE posts.sender IS NULL;
```

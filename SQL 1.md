# Querying
## Database > Spreadsheet?
- Scale: Hold more data
- Frequency: Easier to update data.
- Speed: faster than spreadsheet
## Database
Collection of data organized for creating, reading, updating, and deleting
## Database Management System
Software via which you can interact with a database
## SQL - Structured Query Language
Language to manipulate database

## Keywords
### SELECT
```SQL
-- Selects all columns from table longlist
SELECT * FROM "longlist"; 

-- Select specifc column's from table
SELECT "column1", "column2", ... FROM "table";
```
### LIMIT
```SQL
-- WIll show only 10 rows from the column
SELECT "title" FROM "longlist" LIMIT 10;
```
### WHERE
```SQL
-- WHERE will only show rows of selected columns where year is 2023
SELECT "title", "author" FROM "longlist" WHERE "year" = 2023;

-- Single quotes '' is used to specify a string
SELECT "tile", "format" FROM "longlist" WHERE "format" != 'hardcover';

-- Equivelant to above query
SELECT "tile", "format" FROM "longlist" WHERE NOT "format" = 'hardcover';

-- OR means either can be true
-- AND means both must be true
-- () means that everything inside is part of the condition
SELECT "title", "author" FROM "longlist"
WHERE ("year" = 2022 OR "year" = 2023)
AND NOT "format" = 'hardcover';
```
### NULL
```SQL
-- NULL means it does not exist
-- IS is a keyword that means equals
-- IS NOT means !=
SELECT "title", "translator" FROM "longlist"
WHERE "translator" IS NULL;
```
### LIKE
```SQL
-- 'love' must be somewhere in the title
SELECT "title" FROM "longlist"
WHERE "title" LIKE '%love%';

-- "_" means that it can be any character
SELECT "title" FROM "longlist"
WHERE "title" LIKE 'P_re';
```
### Range Conditions
```SQL
-- year is between 2019 and 2022
SELECT "title", "year" FROM "longlist"
WHERE "year" >= 2019 AND "year" <= 2022

-- Same as above
SELECT "title", "year" FROM "longlist"
WHERE "year" BETWEEN 2019 AND 2022
```
### ORDER BY
```SQL
-- Will order by rating in acseding order (least to greatest) by default
SELECT "title", "rating" FROM "longlist"
ORDER BY "rating" LIMIT 10;

-- Will give highest to least
SELECT "title", "rating" FROM "longlist"
ORDER BY "rating" DESC LIMIT 10;
```
### Aggregate
```SQL
-- Calculate average value of ratings
SELECT AVG("rating") FROM "longlist";

-- Rounds the average to 2 decimal points
SELECT ROUND(AVG("rating"), 2) FROM "longlist";

-- names the rounded average rating as "average rating"
SELECT ROUND(AVG("rating"), 2) AS "average rating" FROM "longlist";
```

# Relating
## Relational Database
A database with multiple tables that relate to each other.
## Relationships
- one-to-one
- one-to-many
- many-to-many
## Entity Relationship Diagram
Explains how entities (tables) interact in a diagram
## Key
Allows us to relate tables to one another
## Subqueries
A query performed within a query to help find things like foreign keys to relate tables.
```SQL
-- Example of using subqueries to get foreign keys when no known to relate.
SELECT "title" FROM "books" WHERE "publisher_id" = (
	SELECT "id" FROM "publishers"
	WHERE "publishers" = "Maclehose Press"
);

-- Calculates average ratings of books from other tables
SELECT AVG("rating") FROM "ratings" WHERE "book_id" = (
	SELECT "id" FROM "books" WHERE "title" = 'In Memory of Memory'
)

-- IN is used it is not just 1!!!
SELECT "title" FROM "books" WHERE "id" IN (
	SELECT "book_id" FROM "authored"
	WHERE "author_id" = (
		SELECT "id" FROM "authors" WHERE "name" = 'Fernanda Melchor'
	)
)
```
## JOINS
```SQL
-- Will join two tables together based on id
SELECT * FROM "sea_lions"
	JOIN "migrations" ON "migrations"."id" = "sea_lions"."id";
```
###  INNER JOIN
Only joins is can find matching data otherwise it is not part of the query
### LEFT JOIN
Priority on joining into the left table
### RIGHT JOIN
Priority on joining into the right table
### FULL JOIN
Do no lose any information when joining the tables
## Sets
### UNION
```SQL
-- Display both authors and translators from both tables in the same set
SELECT "name" FROM "translators"
UNION
SELECT "name" FROM "authors";

-- Creates a additional row disinguishing who is a author and translator
SELECT 'author' AS "profession", "name" FROM "translators"
UNION
SELECT 'translator' AS "profession", "name" FROM "authors";
```
### INTERSECT
```sqlite
-- Only will display those who are authors and translators
SELECT "name" FROM "authors"
INTERSECT
SELECT "name" FROM "translator";
```
EXCEPT
```sqlite
-- Wont display those that are authors and translators
SELECT "name" FROM "authors"
EXCEPT
SELECT "name" FROM "translator";
```
## Groups
### GROUP BY
```sqlite
/*
The use of GROUP BY allows us to get the average rating of each book as opposed to getting one rating for all books
*/
SELECT "book_id", AVG("rating") AS "average rating"
FROM "ratings"
GROUP BY "book_id";
```
# Designing
```SQL
--Shows how the database was made
.schema 
```
## Normalization
Creating tables and establishing relationships between them
## CREATE TABLE
```SqLite
-- create a new database
sqlite3 mbta.db

-- Creating table riders with specified columns
CREATE TABLE "riders" (
	"id",
	"name"	
);

-- Connects riders and stations by id
CREATE TABLE "visits" (
	"rider_id",
	"station_id"
)
```
## Data Types and Storage Classes
- NULL - Nothing
- INTEGER - 1, 2, 3, ....
- REAL - More numbers
- TEXT - character
- BLOB - Binary Large Object (binary stuff like images)

Generally, SQlite is not concerned with having us worry about datatype but just the storage class

### Type Affinities
- TEXT
- NUMERIC
- INTEGRER
- REAL
- BLOB

```sqlite
--Deletes the specified table
DROP TABLE "riders";

-- Created a table with type affinities 
CREATE TABLE "riders" (
	"id" INTEGER,
	"name" TEXT,
	PRIMARY KEY("id")
);

CREATE TABLE "stations" (
	"id" INTEGER,
	"name" TEXT,
	"line" TEXT,
	PRIMARY KEY("id")
);

CREATE TABLE "visits" (
	"rider_id" INTEGER,
	"station_id" INTEGER,
	-- All combos would need to be unique!
	--PRIMARY KEY("rider_id", "station_id")
	-- By default SQL gives a primary key we cant see
	FOREIGN KEY("rider_id") REFERENCES "riders"("id"),
	FOREIGN KEY("station_id") REFERENCES "stations"("id")
);
```

### Column Constraints
- CHECK
- DEFAULT
- NOT NULL
- UNIQUE 
### Altering Tables
```sqlite
-- renames a table
ALTER TABLE "visits" RENAME TO "swipes";

-- Rename a column in a table
ALTER TABLE "swipes" RENAME COLUMN "ttpe" TO "type";
``` 
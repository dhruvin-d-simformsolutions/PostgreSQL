# PostgreSQL
  - To open postgres interactive command prompt, type following command
  - sudo su - postgres
  - Enter correct password
  - psql

## Creating database
CREATE DATABASE message_boards;

## Creating users table
CREATE TABLE users (
  user_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  username VARCHAR ( 25 ) UNIQUE NOT NULL,
  email VARCHAR ( 50 ) UNIQUE NOT NULL,
  full_name VARCHAR ( 100 ) NOT NULL,
  last_login TIMESTAMP,
  created_on TIMESTAMP NOT NULL
);

## Inserting data into users table
INSERT INTO users(username,email,full_name,created_on) VALUES ('btholt','lol@example.com','Brain Holt',NOW());

## Showing all records
SELECT * FROM users;
    
## LIMIT
SELECT * FROM users LIMIT 10;

## WHERE
SELECT * FROM users WHERE user_id = 150;
SELECT username,email,user_id FROM users WHERE last_login IS NULL LIMIT 10;

## AND
SELECT username,email,user_id FROM users WHERE last_login IS NOT NULL AND created_on<NOW() - interval '6 months' LIMIT 10;

## ORDER BY
  - ASC : Ascending order
  - DESC : Descending order
SELECT user_id,email,created_on FROM users ORDER BY created_on ASC LIMIT 10;
SELECT user_id,email,created_on FROM users ORDER BY created_on DESC LIMIT 10;

## COUNT
SELECT COUNT(*) FROM users;

## UPDATE
UPDATE users SET last_login = NOW() WHERE user_id = 1 RETURNING *;
UPDATE users SET full_name = 'dhruvin' WHERE user_id = 2 RETURNING *;

## DELETE
DELETE FROM users WHERE user_id=1000;

## ON DELETE CASECADE
  - ON DELETE CASCADE lets PostgreSQL know what to do if the user gets deleted. So if a user makes a comment on the message board and then deletes their account, what do you want it to do? If you omit the ON DELETE CASCADE part, it's the same as doing ON DELETE NO ACTION which means it'll just error and not let you delete the user until you've deleted all the comments first. You can also do ON DELETE SET NULL which means it'll make the user_id null on any comment that was made by that user.

## AS
SELECT comment_id,users.user_id,LEFT(comment,20) AS preview FROM comments WHERE board_id = 39 LIMIT 5;

## JOINS
SELECT comment_id,comments.user_id,users.username,time,LEFT(comment,20) AS preview FROM comments INNER JOIN users ON comments.user_id = users.user_id WHERE board_id = 39;
  
## Sub Queries
SELECT comment_id,user_id,LEFT(comment,20) FROM comments WHERE user_id = (SELECT user_id FROM users WHERE full_name ='Suzanna Siviour');

## Group By
SELECT boards.board_name,COUNT(*) AS comment_count FROM boards NATURAL INNER JOIN comments GROUP BY boards.board_name ORDER BY comment_count DESC LIMIT 10;
SELECT boards.board_name,COUNT(*) AS comment_count FROM boards NATURAL INNER JOIN comments GROUP BY boards.board_name ORDER BY comment_count ASC LIMIT 10;

## Working with JSON
SELECT Distinct CAST(content -> 'type' AS TEXT) FROM rich_content;
  
SELECT Distinct content ->> 'type' AS TEXT FROM rich_content;

SELECT Distinct CAST(content -> 'url' AS TEXT) FROM rich_content WHERE rich_content.content -> 'url' IS NOT NULL;

SELECT content -> 'dimensions' ->> 'height' AS height,content -> 'dimensions' ->> 'width' AS width FROM rich_content WHERE content -> 'dimensions' IS NOT NULL;

## Indexes
Create Index on comments (board_id)
EXPLAIN SELECT comment_id, user_id, time, LEFT(comment, 20) FROM comments WHERE board_id = 39 ORDER BY time DESC LIMIT 40;

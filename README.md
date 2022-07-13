# Railsguides Active Record
---
**Active Record** = "M"VC = **model** which represent business data and logic. 
**ORM** = Object Relational Mapping which is a technique that connects the rich objects of an application to tables in a relational database management system. 

### Naming conventions
- **Model Class** - **_singular_** with first letter of each word **_CamelCase_** (ex: BookClub)
- **Database Table** - **_plural_** with underscores separating words **_snakecase_**(ex: book_clubs)

### Schema conventions
- **Foreign keys** - singularized_table_name_id (ex: article_id, comment_id)
- **Primary keys** - integer column named id as table's primary key (bigint for PostgreSQL and MySQL, integer for SQLite)
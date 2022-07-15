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

### Overriding naming conventions
```ruby
class product < ApplicationRecord
  self.table_name = "my_products"
end
```
need manually define the class name that is hosting the fixtures (my_products.yml) using the set_fixture_class method in test

```ruby
class ProductTest < ActiveSupport::TestCase
  set_fixture_class my_products: Product
  fixtures :my_products
  # ...
end
```

#### Create
**new** method return a new object while **create** return the object and save it to the database.
If a block is provided, both new and create will yield the new object to that block for initialization.

#### Read
below are few examples of different data access methods provided by Active Record.
```ruby
users = User.all
user = User.first
jen = User.find_by(name: 'Jen')
users = User.where(name: 'Jen', occupation: 'RoR developer').order(created_at: :desc)
```
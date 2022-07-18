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

#### Update
```ruby
user = User.find_by(name: 'Jen')
user.name = 'Jennifer'
user.save
# equals to below
user = User.find_by(name: 'Jen')
user.update(name: 'Jennifer')
```
To update several attributes use **update_all**
```ruby
User.update_all "max_login_attempts = 3, must_change_password = 'true'"
# same as below
User.update(:all, max_login_attempts: 3, must_change_password: true)
```

#### Delete
```ruby
user = User.find_by(name: 'Jen')
user.destroy
```
To delete several records in bulk, use **destroy_by** or **destroy_all**
```ruby
# delete all users named Jen
User.destroy_by(name: 'Jen')

# delete all users
User.destroy_all
```

#### Validations
**save!** and **update!** are stricter that they raise the exception _ActiveRecord::RecordInvalid_ if validation fails.
```ruby
class User < ApplicationRecord
  validates :name, presence: true
end

irb> user = User.new
irb> user.save
=> false
irb> user.save!
ActiveRecord::RecordInvalid: Validation failed: Name cannot be blank
```
#### Migration
use **bin/rails db:migrate** to create the table and **bin/rails db:rollback** to roll it back.
# Railsguides 
## Active Record Basics
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

## Active Record Migration
---
#### Migration Overview
migration, using DSL (Domain Specific Language) and allowing schema and changes to be database independent = new version of the database. 

use **reversible** when we wish for a migration to do sth that Active Record doesn't know how to reverse.

Alternatively, can use **up** and **down** instead of **change**

#### Creating a migration
The name of the file is YYYYMMDDHHMMSS_create_products.rb and should define class CreateProducts. 

If the migration name if "AddColumnToTable" or "RemoveColumnFromTable" and is followed by a list of column names and types then migration will contain the appropriate **add_column** and **remove_column**. Example as below
```ruby
$bin/rails generate migration AddPartNumberToProducts part_number:string
# will generate 
class AddPartNumberToProducts < ActiveRecord::Migration[7.0]
  def change
    add_column :products, :part_number, :string
  end
end
# can generate more than one column
$bin/rails generate migration AddDetailsToProducts part_number:string price:decimal
# will generate
class AddDetailsToProducts < ActiveRecord::Migration[7.0]
  def change
    add_column :products, :part_number, :string
    add_column :products, :price, :decimal
  end
end
```
If migration name is "CreateXXX" and is followed by a list of column names and types then will create a table XXX with the columns listed. 
```ruby
$bin/rails generate migration CreateProducts name:string part_number:string
# will generate
class CReateProducts < ActiveRecord::Migration[7.0]
  def change
    create_table :products do |t|
      t.string :name
      t.string :part_number

      t.timestamps
    end
  end
end
```
generator accepts column type as **references** and **belongs_to**. **references** will create a user_id column, are a shorthand for creating columns, indexes, foreign keys or even polymorphic association columns.
```ruby
$bin/rails generate migration AddUserRefToProducts user:references
# will generate
class AddUserRefToProducts < ActiveRecord::Migration[7.0]
  def change
    add_reference :products, :user, foreign_key: true
  end
end
```
produce join tables
```ruby
$bin/rails generate migration CreateJoinTableCustomerProduct customer product
# will generate
class CreateJoinTableCustomerProduct < ActiveRecord::Migration[7.0]
  def change
    create_join_table :customers, :products do |t|
      # t.index [:customer_id, :product_id]
      # t.index [:product_id, :customer_id]
    end
  end
end
```
#### Generate model
can append as many column name/type pairs as we want
```ruby
$bin/rails generate model Product name:string description:text
# will generate
class CreateProducts < ActiveRecord::Migration[7.0]
  def change
    create_table :products do |t|
      t.string :name
      t.text :description

      t.timestamps
    end
  end
end
```
add index on the new column
```ruby
$bin/rails generate migration AddPartNumberToProducts part_number:string:index
# will generate
class AddPartNumberToProducts < ActiveRecord::Migration[7.0]
  def change
    add_column :products, :part_number, :string
    add_index :products, :part_number
  end
end
```
# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) SOFTWARE ENGINEERING IMMERSIVE

# SnackAttack Rails App!

![](https://media2.giphy.com/media/xUOxfaABfkwPSJ5M6A/giphy.gif)

## Setup


* `rails new . -M --skip-active-storage --api --database=postgresql`
* `rails db:create`

## Let's get some Snacks!

* Let's start off by using `rails g model` to make a Snacks model.
  * The model should also have a column for name (string) and calories (integer) 
  * Open up the newest migration in in the `db/migrate` directory.
  ```ruby
    def change
      create_table :snacks do |t|
        t.string :name
        t.integer :calories

        t.timestamps 
      end
    end
  ```
* `rails db:migrate`
* Create some seeds in `db/seeds.rb`
  ```ruby
  Snack.create!(name: 'Duane Reade Snack Mix', calories: 100)
  Snack.create!(name: 'Duane Reade Rocky Road Ice Cream', calories: 300)

  Snack.create!(name: 'Microwave Pizza', calories: 400)

  Snack.create!(name: 'M&Ms', calories: 300)
  Snack.create!(name: 'Skittles', calories: 300)
  Snack.create!(name: 'Snickers', calories: 300)

  puts "created #{Snack.count} snacks!"
  ```
* `rails db:seed` will run this file!  Now we have stuff in our database!
* Open up a new *tab* in your terminal.  Run `rails c` (short for `rails console`)
* Test out the following prompts. Check out the SQL query that gets logged for each command. Record the SQL query in the `seed.sql` file and comment them out (so they don't raise errors).
* Prompts:
  * `Snack.count`
  * `Snack.first`
  * `Snack.find_by(name: 'M&Ms')`
  * `Snack.where(calories: 300)`
  * `Snack.where(calories: (200..500))`
  * `Snack.where.not(calories: (200..500))`
* SOOO COOOLLL
* Now let's try to update some records.
  * Assume we mis-entered the number of calories for M&Ms
  * `snack = Snack.find_by(name: 'M&Ms')`
  * `snack.update!(calories: 200)`

### Adding more stuff

Looks like a log of these snacks have some Brands in common.  Lets create a `brands` model!

* Use `rails g model` to add a Brands model.
* The brands table should have name (string), logo_url (string), and a reference to snacks.
* Open up the newest migration in in the `db/migrate` directory.
```ruby
  def change
    create_table :brands do |t|
      t.string :name
      t.string :logo

      t.timestamps
    end
  end
```
* `rails db:migrate`

## Now let's add some Brands

First let's start with a clean slate and destroy every `Snack`.  In the console run `Snack.destroy_all`. We are safe doing this today because we are just playing around with our seed data. In general this is *not* a safe action (specially in a production database).

## Relations

A single brand can have many snacks. Would you agree? Wouldn't it be nice to link a snack to its brand?

* `rails g migration AddBrandRefToSnacks brand:references`

Now in the last migration:

```ruby
  def change
    add_reference :snacks, :brand, null: false, foreign_key: true
  end
```
* `rails db:migrate`

### `has_many` and `belongs_to`

Now to actually link these models together we just need two easy things.

`app/models/brand`
```ruby
  class Brand < ApplicationRecord
    has_many :snacks
  end
```

`app/models/snack`
```ruby
  class Snack < ApplicationRecord
    belongs_to :brand
  end
```

Now let's make some brands!

Now let's update our seeds file so the relations exist.  We will add a `brand` to each snack.

```ruby
mars = Brand.create!(name: 'Mars', logo: 'https://i.ytimg.com/vi/FW0kUmoTQFM/maxresdefault.jpg')
digiorno = Brand.create!(name: 'DiGiorno', logo: 'http://www.digiorno.com/Content/Images/header/DiGiornoLogo.png')
duane_read = Brand.create!(name: 'Duane Reade', logo: 'https://upload.wikimedia.org/wikipedia/en/thumb/0/06/Duane_Reade_Logo.svg/849px-Duane_Reade_Logo.svg.png')
ari_co = Brand.create!(name: 'Ari Co', logo: 'http://aribrenner.com/media/images/ari0.jpg')

pretzel_mix = Snack.create!(name: 'Nice! Pretzel Mix', calories: 100, brand: duane_read)
ice_cream = Snack.create!(name: 'Nice! Rocky Road Ice Cream', calories: 300, brand: duane_read)
pizza = Snack.create!(name: 'Microwave Pizza', calories: 400, brand: digiorno)
mms = Snack.create!(name: 'M&Ms', calories: 300, brand: mars)
skittles = Snack.create!(name: 'Skittles', calories: 300, brand: mars)
snickers = Snack.create!(name: 'Snickers', calories: 300, brand: mars)
```

Now let's run our seed again: `rails db:seed`

And let's hop back in the console:

```ruby
reload! # reload all the new code

snack = Snack.find_by(name: 'M&Ms')
snack.brand
# => the mars model! magic! no SQL needed! how cool!?

brand = Brand.find_by(name: 'Mars')
brand.snacks # => look at all these snacks! so coooll!!
```
* again, record the SQL responses in the seed file and comment them out.

## Deliverable

 - Correctly setup Schema and Models
 - Adding the correct seed data in `db/seeds.rb`
 - Adding the correct SQL logs to the `db/seeds.rb` file
    - SQL logs are given in terminal by the prompted ActiveRecord commands.

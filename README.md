# Rails from Scratch 101

We're going to take a high level overview of Rails and the way it works. This is intended to see how the files connect, and not necessarily intended to be a complete guide to making a Rails app.

## Before You Begin
If you haven't already, make sure to install the Rails and Postgres gems. Navigate through the terminal to the folder you want to work in and enter the following commands:

`gem install rails`

`gem install pg`

If you have any errors while installing these gems, Google the error messages and see if you can work through them. If you can't work through the errors, reach out to an instructor for help.

## Getting Started
Once we've got the Rails and Postgres gems installed, we're ready to create our app by entering the following command:

`rails new app_name --api --database=postgresql`

This will build out a complete file/folder structure for a blank Rails server. Once the app has been created, we need to `cd` into it. Once inside the folder, we can run `rails db:create` to create the database for our application. It will automatically be named after our app's name. These can be found in Postico or psql as `app_name_test` and `app_name_development`, but we only need to focus on the `development` one.

# You Do

Take a moment to create a Rails app by following the above instructions, so we can look at and discuss the file structure as a group.

## The Directory Structure

Let's take a look at the directory contents—there's a lot here but luckily, most of these files don't need to be touched at all. We will primarily be working in four spots in this file structure:

![](./rails_directory.png)

The directory names highlighted in the image above should conceptually look familiar. We will go over these four directories more in depth over the next week but for now, let's take a brief overview of them. In Rails, our controllers and routes are defined separately, and our models and database are defined separately. 

> So how is the functionality split up with these files?
 
1. Our routes are defined in a single file that is located inside of the `/config/` directory. Here, we can tell our Rails server to look for any HTTP method on a route (url path) we define ourselves. We also provide a controller and method for each path.

2. Our controllers are found inside of the `/app/` directory. Typically, we will have a controller for each of our models. We *do* have to freedom to make these however we want though.

3. Also in our `/app/` directory, we can find our models. This is where we define rules for how we interact with our database. This is **NOT** where we define what our tables look like. For example, we may want to define that a user's email is formatted correctly or their username is unique. We also define our associations here in this directory.

4. Finally, our `/db/` directory is where we define our tables and columns. We will be using a new technique for doing so, called a **migration**. We will create migration files that Rails will use as a blueprint to create our database. These migration files come with some specific rules to follow, but also solve a major problem for production databases that we have not yet covered in the course.

## Databases and Migrations

In Rails, we use migration files to track the evolution of our database over time. Previously, when we used Sequelize with Express, we had to reset our database **every** time we made a change to a table. This works well enough for us in development, but does not work well for production databases. Facebook does not drop all of their users each time they want to implement a new feature. 

This is where migration files come in handy. A migration will define a specific change to the database, and are kept in a given (ideally chronological) order. By using migrations, Rails can keep track of how the database has been changed and what changes still need to happen without having to reset your entire database. 

> So how do we make a migration file?

Rails has a CLI tool for adding files that we need for our app. We can call it with `rails generator` (or `rails g` for short) followed by the type of file that we need. In this case, we can use:

```shell
rails generate migration CreateUsers username age:integer is_admin:boolean
```

`CreateUsers` is the name of our migration and `username age:integer is_admin:boolean` are our options for the migration file. We have omitted the `:string` for username, since string is the default type for a column.

This will make a new file in `/db/migrate`:

![](./new_migration.png)

Our new file looks like this:

![](./migration_file.png)

We can see that the command automatically filled in the migration with items that resemble what we put into our command line. Once we `CreateUsers`, it is defined as a class with the same name. We also have a `change` method defined here that runs `create_table`. Inside of the do/end block, we see lines that resemble our columns. This migration is now ready to go, and we can use it to modify our database.

> But how did Rails know how make our migration file?

`rails g migration` has specific syntax that it looks for. A great resource for these keywords can be found in the rails docs here: [Rails Migrations](https://guides.rubyonrails.org/active_record_migrations.html) There are keywords for almost everything that you would ever want to do. Create table, drop table, add column, add foreign reference keys, change column name or data type. They're all available to you.

> But what if I want to do something different? 
> 
> \*\*or rather I can't find the syntax for what I want.\*\*

We can always make a blank migration and call it whatever we want. If we ever add a name that Rails can't parse into keywords, it will still make a migration for us with a class and a `change` method. Upon creating the migration, we're able to add whatever changes we want.

Once we are happy with the way our migration files look, we are ready to run them. However, be aware that once we have run these files, we are committed to them. We cannot edit these files after they have been migrated to our database. This is very important. We cannot edit past migrations! **We cannot edit past migrations!** **WE CANNOT EDIT PAST MIGRATIONS!** Once we have migrated these files, the only way to make changes to our database will be through new migrations. Rails needs the old migration files intact to keep track of how our database is shaped at any given point.

> So how do we run these migration files?

Rails has built-in scripts that we can run. We have already used one to create our database: `rails db:create`. To run our migration files, we run something similar:

```shell
rails db:migrate
```

This will run all of our migration files that haven't been previously run. Since this is our first migration, we will see a new file in our `/db/` directory.

![](./schema_file.png)

The schema file represents what our database looks like currently. We can always refer back here to see how our tables are currently set up.

# You Do

At this point we should have a "users" table. On your own, try to make two new migrations.

1. Make a migration that creates a "posts" table. The table should have a `content` column that is a `string`, as well as a reference key to the "users" table. You can do this by adding `user:references` to the CLI command (`rails g migration etc.`.
2. Make a migration that creates a "comments" table. This table should also have `content` with a type of `string`, and should reference both the "users" table and the "posts" table.
3. Once you're happy with the way that they look, migrate your files.

Once you've completed this exercise, let's talk about how this went. Were there any issues that you ran into? Did the commands work the way that you expected them to?

## Models

We need to create a model for any table that we want to access in our controllers. We can also use a Rails generator to create our models for us as well. One syntax difference here though is that our models are going to be singular as opposed to plural like in the migrations: `rails g model User`. By default, Rails will also generate a migration along with our models. We can simply pass the same options for our table columns after the model name. (yes, we could have done this instead of making our previous migrations. Typically that's how it's done but some extra practice doesn't hurt).

```shell
rails g model User --skip-migration
rails g model Post --skip-migration
rails g model Comment --skip-migration
```

Normally, we won't use the `--skip-migration` flag on this command, but we've already generated our migrations for these models.

Let's make a categories model for our app but include options for a migration file as well.

```shell
rails g model Category topic post:references
```

Now, if we check our files, we'll see that a model was created, as well as a migration file. Since we have a new migration, let check the file. If it looks good, let's run our migration with `rails db:migrate`.

We now have several model files located in `/app/models`. Right now,they are empty. We can fill those in with validations and associations. We have foreign reference keys in our tables but we still need to add rules in our model so that Rails know which tables are associated. Let's start with the User model.

```rb
class User < ApplicationRecord
  validates :username, uniqueness: true, presence: true
  has_many :posts, dependent: :destroy
  has_many :comments, dependent: :destroy
end

```

We have a few things going one here. First is some validations on the username. It has to be unique and cant be `nil`. Find more options for validations in the docs: [rails validations](https://guides.rubyonrails.org/active_record_validations.html). We have also set some associations. On our associations we have set the paired entry in the child table to be deleted when the parent user is deleted. Since these associations are bi-directional, we also need to define the corresponding association in the other models.
```rb
class Post < ApplicationRecord
  belongs_to :user
end

```

Take note of how the `has_many :posts` association is plural and the `belongs_to :user` association is singular.

# You Do

On your own, finish setting up the model files.

1. Add any missing associations (check your migration files for a hint).

2. Also, take a look at the validations available to us in the docs and pick out one to add to one of the models.


## Routes

So far, We've spent a lot of time on the "M" in "MVC". Since React will take care of our view, the only piece we haven't talked about is the controllers. For this, we will actually start with our routes. We can find our `routes.rb` file in `/config/`. Here, we can define which routes we want for our server. Since we are now up to 4 tables that probably all would need full CRUD, we have a lot of routes to define. Luckily for us, Rails provides us with some tools to make this easy for us. It can even handle associations for us. Let's fill in our routes file with this: 

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
  resources :users do
    resources :posts
    resources :comments
  end
  resources :categories
end

```

If we ever want to check what our routes are, we can run the CLI command `rails routes`. Running that now will return something like this:

We are only concerned about the area highlighted in red. Here we can see some of the Rails magic at work. We have all of the conventional routes made for us. Each column here is labeled at the top.
- We can ignore the "prefix" column as that is mostly used for server side rendering which we're not doing.
- Then we see the "Verb" column which is all of the HTTP methods for each route.
- We have our paths under the "URI Pattern" column.
- Lastly the "Controller#Action" column shows which controller Rails will route the requests to and the method that it will call.

## Controllers

Typically, our controller methods are where we access our database and return data. For this, we will need the Ruby ORM, ActiveRecord. We will be covering ActiveRecord in another lesson. For now, let's go over some of the other pieces of controllers.

### Method Actions

- Index: This is for our "find all" route. Find all of the table entries and return them.
- Show: This is for our "find one" route. Find one entry from this table that matches the :id sent in the params.
- Create: Make a new entry in our table using the request body params.
- Update: Find an entry using the :id and update it using the request body.
- Destroy: Find the entry and delete it.

### Params

We can at any time access a param from either our path or our request body by calling `params[:key_name]`

Example: 

```rb
def update
  puts "the id in the path is #{params[:id]}"
  puts "the username in the request body is #{params[:username]}"
end

```

### Response

We can send a response from our controller methods by calling `render` followed by `json:` for a json response.

Example:

```rb
def show
  @database_users = [{name: "Soleil"}, {name: "Joe"}, {name: "David"}]
  @user = @database_users.find{ |user| user.name == params[:name]}
	
  render json: @user
end	

```

# You Do

On your own, fill out the "create" method in the "users_controller.rb". Inside the method, you will be recreating the "rock, paper, scissors" algo. You can get the players choices from the params and render the winner as the response. When you're ready to test, start your API with the CLI command `rails server` and use Insomnia to send a request.

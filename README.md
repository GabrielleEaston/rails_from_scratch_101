# Rails from Scratch 101

We're going to make a basic micro-blogging app where Users can write posts. 

## Before You Begin
If you haven't already, make sure to install the rails and postgress gems. Navigate through the terminal to the folder you want to work in and enter the following commands:

`gem install rails`

`gem install pg`

If you have any errors while installing these gems, Google the error messages and see if you can work through them. If you can't work through the errors, reach out to an instructor for help.

## Getting Started
Once you've got the rails and postgres gems installed, you're ready to create your app by entering the following command:

`rails new blog_app --database=postgresql`

Once the app has been created, make sure to `cd` into it. Once inside the folder, run `rails db:create` to create the database for your application. It will automatically be named after your app's name.

## Makin' Models
Our app will have two models: User and Post. Users will have many posts, and each post will belong to a user. Let's start by generating our User model. In your terminal, write the following:

`rails g model User name bio:text password_digest`

Now, let's generate our Post model:

`rails g model Post title body:text user:references`

Great! Open your Rails app in your text editor, navigate to the `db` folder, and open the `migrate` folder within. There should be two files in the `migrate` folder. The file names should have a timestamp, then the words `create_users.rb` and `create_posts.rb`. Make sure that the code within looks relatively similar to the following, and make any changes that you need to make now:

```
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :name
      t.text :bio
      t.string :password_digest

      t.timestamps
    end
  end
end
```

```
class CreatePosts < ActiveRecord::Migration[5.2]
  def change
    create_table :posts do |t|
      t.string :title
      t.text :body
      t.references :user, foreign_key: true

      t.timestamps
    end
  end
end
```

If everything looks good, go back to the terminal and type `rails db:migrate`. Once you have completed this step, DO NOT edit these migration files again. If you want to change your models in the future, it's better practice to make a new migration that updates your models rather than trying to change these original migrations.

Looking at your file / folder structure again, you should see that a new file has appeared: `schema.rb`. Click on this and take a look at the tables that have been generated for your User and Post models. Thanks Rails!

Next, open the `app` folder and navigate to the `models` folder. Open `post.rb` and make sure you see the following code:

```
class Post < ApplicationRecord
  belongs_to :user
end
```

Now open `user.rb` and update it so that it says:

```
class User < ApplicationRecord
  has_many :posts
end
```

Awesome! We've got our User and Post models, and we've set the one-to-many relationship between them. Let's make some routes!

## Setting Routes
Navigate to the `config` folder and open `routes.rb`. You can set all of your CRUD routes with the following code:

```
resources :users do
  resources :posts
end
```

Save the file, go back to your terminal, and write `rails routes`. This will allow you to see all the routes your app now has available. On the right-most column, you'll see the controllers and actions for these routes. Let's set those up.

## Creating Controllers






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

Great! Open your Rails app in your text editor, navigate to the `db` folder, and open the `migrate` folder within. There should be two files in the `migrate` folder. The file names should have a timestamp, then the words `create_users.rb` and `create_posts.rb`. Make sure that the code within looks relatively similar to the following:

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


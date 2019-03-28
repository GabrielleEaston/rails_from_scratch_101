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

## More Model Stuff!

To help our `password_digest` field work correctly, let's take a moment to add the `bcrypt` gem to our file. Thankfully, Rails already knows we'll probably want this, so all we have to do is go to the `Gemfile` and find `bcrypt`. It should currently be commented out. Simply uncomment it. Then, go to the terminal and type `bundle install`. This command re-installs the gems in your gemfile, so bcrypt will now be added. 

Next, open the `app` folder and navigate to the `models` folder. Open `post.rb` and add the following code (some of it may have been added already by default). The `has_secure_password` line in our User model makes sure that our bcrypt gem works:

```
class Post < ApplicationRecord
  belongs_to :user
end
```

Now open `user.rb` and update it so that it says:

```
class User < ApplicationRecord
  has_secure_password
  has_many :posts
end
```

Awesome! We've got our User and Post models, and we've set the one-to-many relationship between them. Let's add some data!

## Setting Seed Data
Now that we've got these siiiiiiick models, let's seed some data into our app. Copy the content from the `seeds.rb` file *from this Github repository* and paste it in the `seeds.rb` file *in your app*. 

Save your `seeds.rb` file, go to your terminal, and type the following command: `rails db:seed`

Let's open up our rails console and check out our seed data. You can do this by typing `rails console` or `rails c` in your terminal. In the past, you've used raw SQL or Sequelize to query a database. In Rails, we use [Active Record](https://git.generalassemb.ly/wdi-nyc-bananas/LECTURE_U04_D06_Active-Record-101).

Let's try some basic Active Record queries to search our database.
 - `User.all`
 - `User.find(id:1)`
 - `Post.all`
 - `Post.where(user_id: 1)`
 
 Cool! Looks like things are working. Let's keep moving.

## Writing Routes
Navigate to the `config` folder and open `routes.rb`. You can set all of your CRUD routes with the following code:

```
resources :users do
  resources :posts
end
```

Save the file, go back to your terminal, and write `rails routes`. This will allow you to see all the routes your app now has available. On the right-most column, you'll see the controllers and actions for these routes. Let's set those up.

## Creating Controllers
Navigate to the `app` folder and open the `controllers` folder. You need to create 2 new files for this folder: `users_controller.rb` and `posts_controller.rb`

In each of these folders, you'll be making a ruby class that inherits from Application Controller:

```
class UsersController

end
```

```
class PostsController

end
```

Great! Let's add some methods aka "actions" to our new classes.

# Adding Actions
The methods in our classes are going to line up with our app's CRUD actions. We'll be applying our Active Record queries here to make sure that the correct data is pulled from our database at each CRUD action. If you need a refresher, take another look at [Active Record](https://git.generalassemb.ly/wdi-nyc-bananas/LECTURE_U04_D06_Active-Record-101).

Cool, now that we're familiar with Active Record syntax, let's open up our `users_controller.rb` file, add our actions, and assign our instance variables:

```
class UsersController
  def index
    @users = User.all
  end
  
  def show
    @user = User.find(params[:id]) 
  end
  
  def new
    @user = User.new
  end
  
  def create
    @user = User.new(user_params)
    if @user.save
      redirect_to @user
     end
  end
  
  def edit
    @user = User.find(params[:id])
  end
  
  def update
    @user = User.find(params[:id])
    if @user.update_attributes(user_params)
      redirect_to @user
    end
  end
  
  def destroy
    @user = User.find(params[:id])
    @user.destroy
    redirect_to users_path
  end
  
  private

  def user_params
    params.require(:user).permit(:name, :bio, :password)
  end
end
```
Great! We'll need to follow a very similar pattern for our `posts_controller.rb` file. We'll get to that in a few minutes, but first, let's add some views so we can see this app in action.

## Very First View

So we have a database ready to go, and we have our model and controllers set up to ensure the correct data comes into and out of our app. Now we need a place to visualize that data. Let's set up some views for our User model!

Add a folder inside the `views` folder called `users`. Inside of that folder, you'll need to create 4 files:
- `index.html.erb`
- `show.html.erb`
- `new.html.erb`
- `edit.html.erb`

The `.erb` part makes this a special HTML document that will allow us to inject Ruby code into our page. Let's start with our index page. Let's try it out! Open your `index.html.erb` page and enter the following code:

```
<h2>List of Users</h2>
<% @users.each do |user| %>
  <p><%= user.name %><p>
<% end %>
```

Basically, when you want to inject Ruby code, you wrap the code with `<% %>`. Some people think these look like fish and call them "flounders". If you want the output of the Ruby code to be visible on the page, add an equals sign to the first one, like this: `<%= %>`. People like to call the ones with equals signs "squids." Coders... we're a weird bunch. :)

Before we keep going, let's deploy our app to a local server and see if all this stuff is working. To do so, return to your terminal and type `rails server` or `rails s`, then go to your web browser and open `http://localhost:3000/users`.

Looks good! Let's continue by making the rest of our CRUD views. Our `new` and `edit` views will require us to use forms. We could write these in regular HTML, but they'd be pretty complicated with form authenticity tokens. Luckily Rails gives us a much easier way to write forms. We'll dive deeper into forms later in this course, but in the meantime, you can learn more about them in this [Forms and Partials](https://git.generalassemb.ly/wdi-nyc-bananas/rails-form-helpers-partials-lesson) lesson.

#### show.html.erb
```
<h2><%= @user.name %></h2>
<p><%= @user.bio %></p>
```

#### new.html.erb
```
<h2>Create New User</h2>
<%= form_for @user do |f| %>
  <%= f.text_field :name, placeholder: "Name" %>
  <%= f.text_area :bio, placeholder: "Bio" %>
  <%= f.password_field :password, placeholder: "Password" %>
  <%= f.submit "Submit"%>
<% end %>
```

#### edit.html.erb
```
<h2>Edit <%= @user.name %></h2>
<%= form_for @user do |f| %>
  <%= f.text_field :name, placeholder: "Name" %>
  <%= f.text_area :bio, placeholder: "Bio" %>
  <%= f.password_field :password, placeholder: "Password" %>
  <%= f.submit "Submit"%>
<% end %>
```

Good work. Now go back to the browser and give all these pages a try. We haven't added links between our views yet, so you'll have to manually type in the URLS (fill in the `:id` with the actual id number you're looking for):
-`/users/:id`
-`/users/new`
-`/users/:id/edit`

Try creating and editing a new user. Great work! You've made a basic CRUD app in Rails!

## Let's Polish This Turd
There's still a lot we need to add before the User parts of our app are ready:
- Links between our views
- Reducing the redundancy of our New and Edit forms with partials







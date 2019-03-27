# Rails from Scratch 101

## Before You Begin
If you haven't already, make sure to install the rails and postgress gems. Navigate through the terminal to the folder you want to work in and enter the following commands:

`gem install rails`
`gem install pg`

If you have any errors while installing these gems, Google the error messages and see if you can work through them. If you can't work through the errors, reach out to an instructor for help.

## Getting Started
Once you've got the rails and postgres gems installed, you're ready to create your app by entering the following command.

`rails new app_name --database=postgresql`

Once the app has been created, make sure to `cd` into it. Once inside the folder, run `rails db:create` to create the database for your application. It will automatically be named after your app's name.


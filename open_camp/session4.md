Session 4 - July 8th
=====================

## Introduction (30 minutes)
#### 1. Welcome and logistics
Enjoy pizza and beer and be sure to introduce yourself to other people learning Ruby on Rails!

While doing that...

  - Join the [RailsMN Google Group!](https://groups.google.com/forum/?fromgroups#!forum/railsmn)
  - Follow [@RailsMN on Twitter!](http://www.twitter.com/railsmn)

We want your feedback! Please reach out via

- Create a [GitHub issue](https://github.com/railsmn/schedule/issues)
- [Derek Rockwell](http://www.twitter.com/derekrockwell) (local in person)
- [Weston Platter](http://www.twitter.com/westonplatter) (remote from CA)

Thanks to our sponsors, Google for Entrepreneurs and CoCo Minneapolis for the awesome space, food and refreshments!


#### 2. [Session 3](https://github.com/railsmn/schedule/blob/master/open_camp/session3.md) - Recap

Last time we covered:

- How to incorporate [Bootstrap](http://twitter.github.io/bootstrap/) to make our application look a little better and use it to create some common website elements (header, footer, etc)
- How to add due date functionality to our tasks
- How to send e-mails with Ruby on Rails 


#### 3. No updates to your Virtual Machine!

Last time we needed to update our Virtual Machine to reflect some changes from Session 1, but not this time! 

If you do not have a virtual machine (maybe this is your first session) then [follow these instructions](https://github.com/railsmn/railsmn-dev-box)


#### 4. Session 4 Overview

In session 4 we will start making Open Camp a little more robust with some cool features, and then deploy it to the internet! Specifically we'll:

- Create some e-mail notifications for upcoming due tasks (that run in the background)
- Deploy to Heroku
- Learn about some cool tools/services that are useful to a Rails developer


## Hack Time (90 minutes)

### 1. Email Alert on Task Creation

As an example of what we can do with Rails and ActionMailer (how Rails sends E-mails), let's create e-mails that get sent to the owner of a Task when a Task is created. The first step is straight forward and we will run the Rails generator which will give us the skeleton for our new mailer. In your virtual machine within your project directory ````/vagrant/open_camp```` run:

```
rails generate mailer task_mailer task_creation
```

The output of that command gave us a few new files: ````task_mailer.rb```` and ````task_creation.text.erb````. The first is the 'class' that our code will use to generate the e-mails, and the latter is the file we will edit to determine what our e-mail will say.

#### ````task_mailer.rb```` ###
Let's open up ````app/mailers/task_mailer.rb```` and modify a couple things to our liking.


* Change the line that starts with "default from: ..." and modify it to be:
    
``` ruby
default from: "reminders@opencamp.com"
```

* Change the ````task_creation```` method to look like this:

``` ruby
  def task_creation(task)
    @task = task
    mail to: task.user.email, subject: "New Task Created: #{task.name}"
  end
```

One interesting piece of functionality above is the use of the 'hash' symbol, #, to run Ruby code to generate dynamic text. Feel free to ask a mentor about the capabilities of ````#{}````. 

#### ````task_creation.text.erb```` ####
Now open up ````app/views/task_mailer/task_creation.text.erb```` and replace the contents with this:

``` HTML+ERB
A new task has been created for you!

Description: <%= @task.description %>

You may access this Task at <%= task_url @task %>.

```

For more information about how this email was generated, more specifically the URL to the task in the e-mail, check out [the ActionMailer basics tutorial](http://guides.rubyonrails.org/action_mailer_basics.html#generating-urls-in-action-mailer-views).

Make sure to save all of your files. We should be able to create and send e-mails now! 

#### Send on Create ####
To be able to use our e-mails, we need to tell our Task model that when a new task is saved to the database that we want to trigger an e-mail to send to the user. To do this, we'll use the 'after_save' callback within our Task model.

Open up our task model file, ````app/models/task.rb```` and we'll add two things: the after_save callback and a new corresponding function.

Within the ````class Task```` and ````end````, along with the rest of what we have in Tasks, add:

``` ruby
after_save :send_creation_email
```

and

``` ruby
def send_creation_email
  TaskMailer.task_creation(self).deliver
end
```

Your ````task.rb```` file should now look like this:

``` ruby
class Task < ActiveRecord::Base
  attr_accessible :due_date, :description, :name
  belongs_to :user

  validate :due_date_cannot_be_in_the_past
  validates :name, presence: true, length: {minimum: 5, maximum: 30}
  validates :description, presence: true, length: {within: 2..160}
  
  after_save :send_creation_email

  def send_creation_email
    TaskMailer.task_creation(self).deliver
  end

  def due_date_cannot_be_in_the_past
    unless due_date.blank?      
      if due_date < Date.today
        errors.add(:due_date,"cannot be in the past")
      end
    end
  end
end
```

That'll do it! In summary we:

  1. Ran the Rails mailer generator to create our files
  2. Updated our files to reflect what should be sent
  3. Updated our task model to send e-mails after the task is created

#### Test it! ####

Now go to your task creation page and create a task. When you access Mailtrap (as configured from Session 3) you should see the e-mail corresponding to your new task. 

NOTE: If you do not see the e-mail, check to make sure your Mailtrap settings are correct in your ````app/config/environments/development.rb```` file. If you pulled down the Session 3 code, the default is set to be Derek's Mailtrap configuration.

### 2. Deploy to Heroku ###

We're at a point where it may be time to learn how to deploy our code to the internet for all to see! To do so, we'll use a very popular service (with a free tier that fits our needs quite well) called [Heroku](http://www.heroku.com).

Heroku is a 'Platform as a Service' that makes it simple to push our code and do a lot of the deployment configuration for us so we only need to worry about our code, not our servers. 

#### 1. Create an account ####

Go to [the Heroku signup page](https://id.heroku.com/signup) and sign up for a Heroku account. You'll receive an e-mail confirmation.

Once you create your account and see the 'Apps' dashboard you need to create an app that we can push to. In our case, we took open-camp, which means we can deploy to http://open-camp.herokuapp.com.

#### 2. Set up Heroku ####

Go into your ```Gemfile``` and add Heroku:

```
gem "heroku"
```

Install Heroku by running within your terminal:

```
bundle install
``` 

Once that finishes, log in to Heroku. You'll be prompted for your e-mail/password and you should enter 'Y' when asked about a public key.

```
heroku login
```

##### Heroku Application Changes #####
With the way Heroku is set up, we need to make a couple minor modifications to Open Camp to support Heroku.

Open up ````config/application.rb```` and add the following line. You can put it anywhere inbetween ````module OpenCamp```` and the second-to-last ````end```` at the bottom of the file:

```
config.assets.initialize_on_precompile = false
```

The next change is due to the fact that Heroku is starting our application in 'production' mode, versus 'development' mode which we've been doing on our machine. This is an issue that needs to be fixed so we can still send e-mails on Heroku.

In session 3 we added some lines to our ```config/environments/development.rb``` file, namely the following settings:

```
config.action_mailer.default_url_options
config.action_mailer.delivery_method
config.action_mailer.smtp_settings
```

You'll need to copy those 3 settings from your development.rb file into your ```config/environments/production.rb``` file to get things to work.

#### 3. Using Git ####

We've talked a bit about Git but we haven't used it to it's potential quite yet, but that's about to change. For all the work you've done this evening you'll need to check it in to your local 'Git' repository. The following can be tricky and only covers a couple of the various scenarios at this point, so if you run into issues, ask a mentor for guidance.

If you want to learn some more Git (highly recommended), we suggest you check out [CodeSchools free interactive 15-minute Git tutorial](http://try.github.io/levels/1/challenges/1).

##### Save your progress #####
We'll need to 'check out' a new branch so we don't conflict with anything from GitHub. To do so:

```
git checkout -b opencamp_heroku
```

Next, save your progress to your new branch:

```
git add .
git commit -am "Initial commit for heroku"
```

##### Set up your Heroku apps Git repository #####

If you run:

```
heroku apps
```

In your terminal you should see the app you created earlier. Didn't create one? Go back to [Heroku](http://www.heroku.com) and create one. 

Noting the name of your app, you should be able to run: 

```
heroku info -a (your-application-name)
```

This will give you information like the Web URL for your application, but more importantly what we want is the "Git URL". Copy the Git URL returned from the above command, in our case it was ````git@heroku.com:open-camp.git````.

With that, run:

```
git remote add heroku (copied git@... string from above)
```

We should be good to go!

##### Push to Heroku #####

Now that we have saved our changes and set up the Heroku repository, let's push our changes up to the Heroku repository. If all went well, we should be able to run (answer yes about RSA keys):

```
git push heroku opencamp_heroku:master
```

If that command is successful you should see some output to the effect of (don't run this):

```
Counting objects: 137, done.
Compressing objects: 100% (85/85), done.
Writing objects: 100% (104/104), 11.40 KiB, done.
Total 104 (delta 61), reused 36 (delta 19)

-----> Ruby/Rails app detected
-----> Using Ruby version: ruby-1.9.2
-----> Installing dependencies using Bundler version 1.3.2
```

This command will take a little bit to run as it brings in all of the app dependencies and compiles our assets and then starts Rails.

Once this is done and says 'launching' you can run:

```
heroku open
```

##### Update your Heroku database #####
Just like on our local machines, we need to run all of the migrations we've had so far, except on Heroku for our application to work. To do so:

```
heroku run rake db:migrate
```

This command will take a little bit to run and when it is complete your application will be available at http://(your-app-name-here).herokuapp.com

### Development Tools ###
Now that we've been developing with Ruby on Rails for a while, there are a couple tools out there that make development (specifically debugging) much easier. Some of these that we use are:

* Better Errors - when your application has an error, the browser will offer a much more detailed view of what happened
* Binding of Caller - enhances Better Errors by giving you a console that you can type Ruby code into when there was an error to see what exactly happened
* Pry - a better console that gives you a bunch more functionality when you run 'rails console' and also allowing you to set 'breakpoints' in your code where you can stop your code at a certain point to inspect what is happening
* Meta Request - adds the ability to use the Google Chrome web inspector via RailsPanel to see what is happening with your Rails app
* Google Chrome web inspector - built within Chrome to show you what a website is doing at the front-end (HTML, CSS, JavaScript)

For some great resources about how to use these tools, check out the relevant RailsCasts writeups here:

* [Better Errors, Binding of Caller and Meta Request/RailsPanel](http://railscasts.com/episodes/402-better-errors-railspanel?view=asciicast)
* [Pry with Rails](http://railscasts.com/episodes/402-better-errors-railspanel?view=asciicast)

We definitely encourage you to check out these RailsCasts and start using these tools to make debugging a lot more pleasant.
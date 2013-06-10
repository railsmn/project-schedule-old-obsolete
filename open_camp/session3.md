Session 3 - June 10th
=====================

## Introduction (30 minutes)
#### 1. Welcome and logistics
Enjoy pizza and beer and be sure to introduce yourself to other people learning Ruby on Rails!

While doing that...

  - Join the [RailsMN Google Group!](https://groups.google.com/forum/?fromgroups#!forum/railsmn)
  - Follow [@RailsMN on Twitter!](http://www.twitter.com/railsmn)

We want your feedback! Please reach out via,

- Create a [GitHub issue](https://github.com/railsmn/schedule/issues)
- [Derek Rockwell](http://www.twitter.com/derekrockwell) (local in person)
- [Weston Platter](http://www.twitter.com/westonplatter) (remote from CA)

Thanks to our sponsors, Google for Entrepreneurs and CoCo Minneapolis for the awesome space, food and refreshments!


#### 2. [Session 2](https://github.com/railsmn/schedule/blob/master/open_camp/session2.md) - Recap

Last time we covered:

- [Devise](https://github.com/plataformatec/devise) and how to use it to allow users to create accounts and log in to Open Camp
- Creating relationships between 'objects' in Ruby on Rails. Once we created users, we could then associate them to tasks to create unique tasks for each user in Open Camp
- Testing! We wrote our first unit test and explained the importance of testing in web development


#### 3. No updates to your Virtual Machine!

Last time we needed to update our Virtual Machine to reflect some changes from Session 1, but not this time! 

If you do not have a virtual machine (maybe this is your first session) then [follow these instructions](https://github.com/railsmn/railsmn-dev-box)


#### 4. Session 3 Overview

In session 3 we will cover more of the frontend aspects of Ruby on Rails development. We'll:

- Incorporate [Bootstrap](http://twitter.github.io/bootstrap/) to make our application look a little better and use it to create some common website elements (header, footer, etc)
- Add due date functionality to our tasks
- Learn how to send e-mails with Ruby on Rails 

## Hack Time (90 minutes)

### 1. Integrate Twitter Bootstrap
[__Twitter Bootstrap__](http://twitter.github.io/bootstrap/) is a frontend styling framework created to make it easier to code up sleek and smooth UIs. It's gotten a lot of attention in the last year because it provides [a responsive layout](http://twitter.github.io/bootstrap/scaffolding.html), [common HTML elements](http://twitter.github.io/bootstrap/base-css.html), and [nifty design components](http://twitter.github.io/bootstrap/components.html) through CSS classes.

We'll use Bootstrap to create the basic OpenCamp UI. In this session, we'll focus on creating: 
- content section
- header
- footer
- flash message section

#####Step 0 - Define the app's root path#####

When you start the Rails server and go to [localhost:3000](http://localhost:3000), you see a Ruby on Rails page. This is the default index page for Rails applications. We're going to change this.  
1. Delete the app/public/index.html file.  
2. Add this to the app/config/routes.rb, 

``` ruby
root to: 'tasks#index'
```

#####Step 1 - Install the gem#####

Add the following lines to your ```Gemfile``` file, and install the added gem by running  ```bundle install```  in the terminal.

```
gem 'bootstrap-sass'
```

#####Step 2 - Require the Bootstrap JavaScript#####

The  ```bootstrap-sass```  gem uses JavaScript to accomplish a portion of its styling, which we want to include in our Rails application. We'll include it in the app by adding this line to the application.js ```(app/assets/javascripts/application.js:)``` file,  

```
//= require bootstrap
```

Your  ```application.js```  file should then look like this, 

```
//= require jquery
//= require jquery_ujs
//= require bootstrap
//= require_tree .
```

#####Step 3 - Import the Bootstrap CSS#####

Similar to how we included or required the Bootstrap JavaScript, we need to include the Bootstrap CSS. To keep the CSS organized, we'll create a Bootstrap import and override file to contain this logic.  

Therefore, add this file, ```app/assets/stylesheets/bootstrap_and_overrides.css.scss``` with this content:  

``` scss
@import "bootstrap";
@import "bootstrap-responsive";

html, body {
  height: 100%;
}

/* Wrapper for page content to push down footer */
#wrap {
  min-height: 100%;
  height: auto !important;
  height: 100%;
  /* Negative indent footer by it's height */
  margin: 0 auto -60px;
}

#header {
  padding-bottom: 20px;
}

#push, #footer {
  height: 60px;
}

#footer {
  background-color: #f5f5f5;
}

```

You might be wondering what the  ```scss``` means. SCSS is a file handler defintion. It tells Rails to evaluate file with the SASS rendering engine before using the file's CSS.

Still want to know more about "file handler definition"? A file handler definition tells Rails to evaluate a file with a rendering engine before sending the page back as a response. For example, each file in app/views has  ```.html.erb```  at the end of it. This tells Rails to first evaluate the page with the  ```erb```  rendering engine before sending the HTML back as a response.  


#####Step 4 - Change the default layout#####

Ruby on Rails has the concepts of an 'application layout'. This acts as the skeleton for which all of your pages will fill out (e.g. creating a task, showing tasks, signing up). Since most websites consist of common elements on all of the pages (headers, footers, sidebars) it makes sense to not have to put them on every page, so our application layout is very useful.

For more information on layouts and how they work, check out [the RailsGuides article on layouts](http://guides.rubyonrails.org/layouts_and_rendering.html).

To start, copy and paste the following into application.html.erb (app/views/layouts/application.html.erb).


This adds 3 "render partial" commands. We'll also create the partials in the same step.

``` HTML+ERB
<!doctype html>
<html>
  
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1.0'>
    <title><%= content_for?(:title) ? yield(:title) : 'OpenCamp' %></title>
    <meta name='description' content=''>
    <meta name='author' content=''>
    <%= stylesheet_link_tag 'application', :media => 'all' %>
    <%= javascript_include_tag 'application' %>
    <%= csrf_meta_tags %>
    <%= yield(:head) %>
  </head>
  
  <body>
    <!-- the #wrap div helps us have a sticky footer  -->
    <div id='wrap'>
      <div id='header' class='navbar navbar-static-top'>
        <div class='navbar-inner'>
          <div class='container'>
            <%= render partial: 'layouts/header' %>
          </div>
        </div>
      </div>

      <div id='main'>
        <div class='container'>
          <div class='content'>
            <div class='row'>
              <div class='span12'>
                <%= render partial: 'layouts/messages' %>
                <%= yield %> <!-- this is where the page specific content comes through -->
              </div>
            </div>
          </div>
        </div>
      </div>
      <div id='push'></div> <!-- keep this here. this enables a vertical space to separate #main and #footer -->
    </div>

    <div id='footer'>
      <div class='container'>
        <%= render partial: 'layouts/footer' %>
      </div>
    </div>
  </body>

</html>
```

You'll notice three lines in the code above that say 'render partial: ...'. This just means Rails will look for an HTML file (a layout) where we tell it to and pull in the HTML from that layout and inject it in place of our 'render partial' Ruby code.

We haven't created any of those layouts yet, so let's do that.

Add a header partial file, app/views/layouts/_header.html.erb, and add this content.  

``` HTML+ERB
<%= link_to 'OpenCamp', '/tasks', class: 'brand' %>
<% if user_signed_in? %>
  <ul class='nav'>
    <li>
      <%= link_to 'Tasks', tasks_path %>
    </li>
    <li>
      <%= link_to 'Notes', notes_path %>
    </li>
  </ul>
  <div class='pull-right'>
    <ul class='nav'>
      <li>
        <%= link_to 'Edit account', edit_user_registration_path %>
      </li>
      <li>
        <%= link_to 'Logout', destroy_user_session_path, method: 'delete' %>
      </li>      
    </ul>
  </div>
<% else %>
  <div class='pull-right'>
    <ul class='nav'>
      <li>
        <%= link_to 'Sign up', new_user_registration_path %>
      </li>
      <li>
        <%= link_to 'Login', new_user_session_path %>
      </li>
    </ul>
  </div>
<% end %>
```

Add a footer partial file, ```app/views/layouts/_footer.html.erb```, and add this content.  

``` HTML+ERB
<b>OpenCamp</b> | 
<%= link_to 'About', 'https://github.com/railsmn/open_camp' %>
```


Add messages partial file, ```app/views/layouts/_messages.html.erb```, and add this content.  

``` HTML+ERB
<% flash.each do |name, msg| %>
  <% if msg.is_a?(String) %>
    <div class="alert alert-<%= name == :notice ? 'success' : 'error' %>">
      <a class='close' data-dismiss='alert'>&#215;</a>
      <%= content_tag :div, msg, id: "flash_#{name}" %>
    </div>
  <% end %>
<% end %>
```

#####Step 5 - Remove ```<p id="notice"><%= notice %></p>``` instances#####

We introduced a couple concepts just now. Rails controllers give us the capability of passing messages to our HTML in the form of 'flash' messages. There are several types of flash messages: errors, warnings or notices. For more on flash messages, check out ['the flash'](http://guides.rubyonrails.org/action_controller_overview.html#the-flash).

So we just created a partial to handle rendering flash messages, and we render it from the application.html.erb file (the application's global layout file). This was a good decision because it isolated the logic for rendering flash messages to just once place. Now we need to delete old places where flash messages previously came through the UI:

1. ```app/views/notes/show.html.erb```
2. ```app/views/tasks/show.html.erb```

In both files, delete this line,  
``` HTML+ERB
<p id="notice"><%= notice %></p>
```

#### 2. Form Validation 

Up until now, a user could enter whatever they want (or nothing at all) into our new task form. It would be nice to restrict what kinds of entries we can accept for task names and descriptions, this is where we can add model validation.

Let's add a simple 'presence' validator that simply checks if a task's name and description is provided when we save it. Open up ```app/models/task.rb``` and add:

``` ruby
validates :name, presence: true
validates :description, presence: true
```

Now load your tasks page and try to enter a blank task. You should recive an error message indicating that no name or description was entered.

We can also do things like enforce the length of the content in our names and descriptions by setting minimum and maximum values. In our ```task.rb``` file, change the entries we made above to read:

``` ruby
validates :name, presence: true, length: {minimum: 5, maximum: 30}
validates :description, presence: true, length: {within: 2..160}
```

Note that these are two different ways to handle the same thing, explicitly specifying a minimum and maximum or using within and specifying a range. [Learn more about ActiveModel validators here](http://guides.rubyonrails.org/active_record_validations_callbacks.html#presence).

#### 3. Task due dates

Let's add the ability to associate due dates with our tasks and even add a cool looking date picker to make it easy to use.

###### Step 1 - Update the database

First, we need to add this new 'due_date' field to our database. In the terminal, run a Rails database migration to create this field.

```
rails generate migration AddDueDateToTasks due_date:date
rake db:migrate
```

###### Step 2 - Update the task model

In order to use our new 'due_date' field, we need to let the model know that is okay to change. In ```app/models/task.rb``` we should see a line specifying an ```attr_accessible```. Change it to look like this:

``` ruby
attr_accessible :due_date, :description, :name
```

###### Step 3 - Update our task creation view(s)

Now that our model supports ```due_date``` we can use it on our task creation forms!

We use a partial to handle the new/edit forms for manipulating tasks, so we'll need to add due date functionality to that. Open up ```app/views/tasks/_form.html.erb```. In that file you'll see some form fields corresponding to the name and description we've been working with, and now we want to add due_date. Make sure the file looks like this:

``` HTML+ERB
<%= form_for(@task) do |f| %>
  <% if @task.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@task.errors.count, "error") %> prohibited this task from being saved:</h2>

      <ul>
      <% @task.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :name %><br />
    <%= f.text_field :name %>
  </div>
  <div class="field">
    <%= f.label :description %><br />
    <%= f.text_field :description %>
  </div>
  <div class="field">
    <%= f.label :due_date %><br />
    <%= f.text_field :due_date %>
  </div>    
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

Now we can add due dates to our tasks! Although we can't see them if we click 'show' in our index for a task. Open up ```app/views/tasks/show.html.erb``` and add this below the description paragraph:

``` HTML+ERB
<p>
  <b>Due Date:</b>
  <%= @task.due_date %>
</p>
```

###### Step 4 - It isn't very effective. 

Now we can add the dates to our tasks, but we have to enter in an explicit date format that is difficult to work with, which isn't very friendly. Let's add a date picker from jQuery (a JavaScript helper package, like Bootstrap) to improve the experience.

To do so, let's add ```jquery-ui-rails``` to our Gemfile:

``` ruby
group :assets do
  gem 'sass-rails',   '~> 3.2.3'
  gem 'coffee-rails', '~> 3.2.1'
  gem 'uglifier', '>= 1.0.3'
  gem 'jquery-ui-rails'
end
``` 

Don't forget to re-run ```bundle``` after updating your Gemfile!

Just like earlier, we need to add the jQuery UI datepicker to both our ```application.js``` and ```application.css``` files. Open up ```/app/assets/javascripts/application.js``` and modify it to look like this:

```
//= require jquery
//= require jquery_ujs
//= require bootstrap
//= require jquery.ui.datepicker
//= require_tree .
```

And in ```app/assets/stylesheets/application.css```

```
*= require_self
*= require jquery.ui.datepicker
*= require_tree .
```

Now that our date picker is installed it is time to convert our text box into our date picker by adding some JavaScript. For now, we'll use our ```application.js``` file to do this. Open up ```app/assets/javascripts/application.js``` again and add this to the bottom:

``` javascript
$(document).ready(function () {
  $('#task_due_date').datepicker({dateFormat: "yy-mm-dd"});
});
```

This code will run every time a page is loaded and turn our 'task_due_date' field into a datepicker. You'll note that we specify how we want our dates to be formatted, this is to support the Rails convention of handling dates in a year, month, day format versus the DatePicker's default of month, day, year.

Now when we go to our new/edit forms for tasks we can click on the 'due date' field and we should see a snazzy looking date picker.

###### Step 5 - But, we can go back in time!?

Now that we have date picking functionality, you may note that we can create due dates in the past. This doesn't make sense, and is a perfect case for Active Model validators that we used in the earlier steps. Let's make sure we can't create due dates in the past (but due dates should still be optional).

To do so, we need to add a couple things to our ```task.rb``` file. Open up ```app/models/task.rb``` and add a couple lines:

``` ruby
validate :due_date_cannot_be_in_the_past
```

``` ruby
def due_date_cannot_be_in_the_past
  unless due_date.blank?      
    if due_date < Date.today
      errors.add(:due_date,"cannot be in the past")
    end
  end
end
```

When you're done, your ```task.rb``` file should resemble:

``` ruby
class Task < ActiveRecord::Base
  attr_accessible :due_date, :description, :name
  belongs_to :user

  validate :due_date_cannot_be_in_the_past
  validates :name, presence: true, length: {minimum: 5, maximum: 30}
  validates :description, presence: true, length: {within: 2..160}
  

  def due_date_cannot_be_in_the_past
    unless due_date.blank?      
      if due_date < Date.today
        errors.add(:due_date,"cannot be in the past")
      end
    end
  end
end
```

Now when you enter a date in the past, you should be greeted with an error indicating that you can't do so! There are a couple of other validation errors we can work on, but we'll save those for later.

#### 4. Setup Email System

There are a couple things that would be nice if we could send our users e-mails for. For example, password resets or due date reminders would be ideal. To do this, we'll leverage a web server called Mailtrap.io to capture the e-mails we send so we can look at them in a test environment.

###### Step 1 - Create a Mailtrap account

To get started, [create an account at Mailtrap.io](http://mailtrap.io/).

Once you've created your account you'll notice a page to create inbox. Create an inbox called 'opencamp' for you to use for our project. When it is created, click on the 'opencamp' inbox link and you'll see an interface where e-mails are capture for view.

###### Step 2 - Add your Mailtrap information to your application

On the settings page for your opencamp inbox you'll see 'SMTP credentials' on the right. Click on Ruby on Rails and you should get a popup with directions how to set it up. Copy the code they provide you. Paste that code into your ```app/config/environments/development.rb``` file

You'll also need to tell ActionMailer (the Rails component responsible for handling e-mails) where the 'host' should be. In our case this is ```localhost:3000``` since that is where our server is listening on our machines. To configure this, add this line above where you pasted your Mailtrap configuration code:

``` ruby
config.action_mailer.default_url_options = { :host => 'localhost:3000' }
```

And that's it! Now, when you send password reset requests via the sign in form, Mailtrap will show you the e-mail sent. 

For production environments you could use services like [SendGrid](www.sendgrid.com) or [MailChimp's Mandrill](www.mandrill.com). Both are very easy to incorporate into Rails in a very similar way we did with Mailtrap.

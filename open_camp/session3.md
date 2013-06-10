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

```
root to: 'tasks#index'
```

#####Step 1 - Install the gem#####

Add the following lines to your ```Gemfile``` file, and install the added gem by running,  ```bundle install```  in the terminal.

#####Step 2 - Require the Bootstrap JavasSript#####

The  ```bootstrap-sass```  gem uses JavaScript to accomplish a portion of its styling, which we want to include in our Rails application. We'll include it in the app by adding this line to the ```application.js``` (app/assets/javascripts/application.js:) file,  

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

```
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

```
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

```
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

```
<b>OpenCamp</b> | 
<%= link_to 'About', 'https://github.com/railsmn/open_camp' %>
```


Add messages partial file, ```app/views/layouts/_messages.html.erb```, and add this content.  

```
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
```
<p id="notice"><%= notice %></p>
```


#### 2. Form Validation
#### 3. Setup Email System


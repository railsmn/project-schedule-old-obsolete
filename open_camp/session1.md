# Welcome!  

## Purpose of building OpenCamp / Intro (30 minutes) 

why rails.  

technical principles we will demonstrate.  

explain the difference between websites // web apps (applications).



### Logistics  

1. __Setting up a Rails development env (if you have not installed Rails).__  
Getting Ruby on Rails setup the first time can be tricky. But we have a great solution that makes it easy, [railsmn-dev-box](https://github.com/railsmn/railsmn-dev-box). Checkout the README.md for install setups.

2. __Text editor.__  
You'll want to have good text editor as your create build the OpenCamp rails web app. Here's a couple suggestions per Operating System, but there are many options.

  * Windows  
    ++ [Sublime](http://www.sublimetext.com/)  
    ++ [Notepad++](http://notepad-plus-plus.org/)  
    
  * Mac  
    ++ [Sublime](http://www.sublimetext.com/)  
    ++ [Textmate](http://macromates.com/)

  * Linux  
    ++ [Sublime](http://www.sublimetext.com/)  
    ++ [Gedit](http://projects.gnome.org/gedit/)  


3. __We will be using the command line // terminal a lot.__
Rails utilizes the unix command line // terminal commands. This may be confusing and/or frustrating at first, but we would like to challenge you to embrace the command line operations we walk through. It may be hard at first, but once you get comfortable with the command line operations, you'll feel like a super hero navigating the Rails universe. We promise!

4. __We will be using Git.__  
Git is a code "version control system". In non-technical terms, it iteratively saves changes to your code. The power of git is that you can create checkpoints (by making a "commit") as you write code and transverse back and forth to the various checkpoints (commits). We'll explain this along the way.  



## Session 1 hack-time (1.5 hours)

### Create the OpenCamp rails app

Use the following commands to create the rails app,
    
    # navigate to the railsmn-dev-box folder
    cd ~/railsmn-dev-box
    
    # turn on the VM
    vagrant up

    # SSH into the VM
    vagrant ssh

    # navigate to the vagrant directory
    cd /vagrant

    # create rails app with postgresql database connector configuration
    rails new open_camp -d postgresql

    # navigate to the new rails app
    cd open_camp

    # you have successfully create the rails app



### The ````rake```` command

Rails development use a command line tool called, ````rake````. The name describes it's purpose, ````rake````  = Ruby + ````make````. Let's use it, 
    
    # get project info
    rake about

    # see all possible rake tasks
    rake -T

    # get rake help info
    rake -h


### Database configuration  

Notice that there are 3 different "sections", deveolopment, test, production.  Each represents a Rails environment.  The idea is that you can tell a Rails app to run in development mode, test mode, or production mode, or another custom mode. Each environment or mode has advantages (EG, development mode will print out all SQL queries).

All that aside, we need to change the default values in the ````config/database.yml```` file for each environment.  
1) Change  ````username````  from  ````open_camp````  to  ````vagrant````.  
2) Change  ````encoding````  from  ````unciode````  to  ````ASCII_SQL````.

The following file has the required username and encoding configuration changes.  Copy and paste this file into the database.yml file.

    # PostgreSQL. Versions 8.2 and up are supported.
    #
    # Install the pg driver:
    #   gem install pg
    # On Mac OS X with macports:
    #   gem install pg -- --with-pg-config=/opt/local/lib/postgresql84/bin/pg_config
    # On Windows:
    #   gem install pg
    #       Choose the win32 build.
    #       Install PostgreSQL and put its /bin directory on your path.
    #
    # Configure Using Gemfile
    # gem 'pg'
    #
    development:
      adapter: postgresql
      encoding: SQL_ASCII
      database: open_camp_development
      pool: 5
      username: vagrant
      password:

      # Connect on a TCP socket. Omitted by default since the client uses a
      # domain socket that doesn't need configuration. Windows does not have
      # domain sockets, so uncomment these lines.
      #host: localhost
      #port: 5432

      # Schema search path. The server defaults to $user,public
      #schema_search_path: myapp,sharedapp,public

      # Minimum log levels, in increasing order:
      #   debug5, debug4, debug3, debug2, debug1,
      #   log, notice, warning, error, fatal, and panic
      # The server defaults to notice.
      #min_messages: warning

    # Warning: The database defined as "test" will be erased and
    # re-generated from your development database when you run "rake".
    # Do not set this db to the same as development or production.
    test:
      adapter: postgresql
      encoding: SQL_ASCII
      database: open_camp_test
      pool: 5
      username: vagrant
      password:

    production:
      adapter: postgresql
      encoding: SQL_ASCII
      database: open_camp_production
      pool: 5
      username: vagrant
      password:


### Create Databases  

Create the databases required for developig a rails app (development and test), 

    # --------------------------------------
    # NOTICE !
    # --------
    # RUN COMMAND IN /vagrant/open_camp
    # --------------------------------------
    #
    cd /vagrant/open_camp
    rake db:create


### Create a rails MVC component

We want to create Model, Controller, and View.  This will allow Create/Read/Update/Delete stuff in the web browser and see it in the database.  We'll use the ````rails```` executable, and use the rails' ````generate```` command.
    
    # Explanation behind the command
    # 
    # executable   = rails
    # command      = generate
    # type         = scaffold
    # object       = Task
    # attributes   = name:string description:string
    #
    rails generate scaffold Task name:string description:string

You should the following output in your terminal,  

    invoke  active_record
    create    db/migrate/20130408045955_create_tasks.rb
    create    app/models/task.rb
    invoke    test_unit
    create      test/unit/task_test.rb
    create      test/fixtures/tasks.yml
    invoke  resource_route
    route    resources :tasks
    invoke  scaffold_controller
    create    app/controllers/tasks_controller.rb
    invoke    erb
    create      app/views/tasks
    create      app/views/tasks/index.html.erb
    create      app/views/tasks/edit.html.erb
    create      app/views/tasks/show.html.erb
    create      app/views/tasks/new.html.erb
    create      app/views/tasks/_form.html.erb
    invoke    test_unit
    create      test/functional/tasks_controller_test.rb
    invoke    helper
    create      app/helpers/tasks_helper.rb
    invoke      test_unit
    create        test/unit/helpers/tasks_helper_test.rb
    invoke  assets
    invoke    coffee
    create      app/assets/javascripts/tasks.js.coffee
    invoke    scss
    create      app/assets/stylesheets/tasks.css.scss
    invoke  scss
    create    app/assets/stylesheets/scaffolds.css.scss


Creates a database migration  
Creates a model, ````/open_camp/app/models/task.rb````  
Creates a controller, ````/open_camp/app/controllers/tasks_controller.rb````  
Creates views, ````/open_camp/app/views/*````  


### Run the database migration

Use the  ````rake````  command to run the db migration to create  ````task```` table,  

    # --------------------------------------
    # NOTICE !
    # --------
    # RUN COMMAND IN /vagrant/open_camp
    # --------------------------------------
    #
    # run all outstanding database migrations
    #
    cd /vagrant/open_camp
    rake db:migrate

You should the following output in your terminal,  

    ==  CreateTasks: migrating ====================================================
    -- create_table(:tasks)
    NOTICE:  CREATE TABLE will create implicit sequence "tasks_id_seq" for serial column "tasks.id"
    NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "tasks_pkey" for table "tasks"
      -> 0.0033s
    ==  CreateTasks: migrated (0.0038s) ===========================================


### See it in the browser

Let's test it out the Rails app on your computer's browser,   
    
    # --------------------------------------
    # NOTICE !
    # --------
    # RUN COMMAND IN /vagrant/open_camp
    # --------------------------------------
    #
    # start your local rails server
    # 
    rails server

Now go to [localhost:3000/tasks](http://localhost:3000/tasks)


### Building 2nd Rails MVC - from scratch

Great. Now that we have fully functioning Rails MVC component.  You can Create, Read, Update, and Delete (CRUD) Tasks. Next, let's build another without using the  ````rails generate scaffold````  command.  The  ````scaffold````  command was a great start, but we want to help you think through how the MVC pieces fit together so you can create & code whatever you dream up.


### Model

We'll start with ceating a Model.  

    # --------------------------------------
    # NOTICE !
    # --------
    # RUN COMMAND IN /vagrant/open_camp
    # --------------------------------------
    #
    # generate a model
    # 
    rails generate model Note title:string body:text

You should the following output in your terminal,  

    invoke  active_record
    create    db/migrate/20130408131003_create_notes.rb
    create    app/models/note.rb
    invoke    test_unit
    create      test/unit/note_test.rb
    create      test/fixtures/notes.yml

Notice that the  ````rails generate model````  command created a database migration automatically.  That's because Rails assumes mangement of the database and because all models are 90% of the time mapped back to a database table.  Thus, when we create a model, Rails creates the underlying database table too.  

Notice that the  ````rails generate model````  command also created "test unit files". Rails assumes that you want to use automated testing as you write code.  We'll cover testing later.  Just leave those files for now.  

Open the  ````open_camp/app/models/note.rb````  file just to see what's in there,   

    # open_camp/app/models/note.rb

    class Note < ActiveRecord::Base
      attr_accessible :body, :title
    end

Before we move on to the next part, be sure to run `rake db:migrate` to add the `notes` table to your database.

### Controller

Next, create the controller, 

    # --------------------------------------
    # NOTICE !
    # --------
    # RUN COMMAND IN /vagrant/open_camp
    # --------------------------------------
    #
    # generate a controller with actions
    # 
    rails generate controller Notes index show new edit create update destroy

You should the following output in your terminal,  

    create  app/controllers/notes_controller.rb
     route  get "notes/delete"
     route  get "notes/update"
     route  get "notes/create"
     route  get "notes/edit"
     route  get "notes/new"
     route  get "notes/show"
     route  get "notes/index"
    invoke  erb
    create    app/views/notes
    create    app/views/notes/index.html.erb
    create    app/views/notes/show.html.erb
    create    app/views/notes/new.html.erb
    create    app/views/notes/edit.html.erb
    create    app/views/notes/create.html.erb
    create    app/views/notes/update.html.erb
    create    app/views/notes/delete.html.erb
    invoke  test_unit
    create    test/functional/notes_controller_test.rb
    invoke  helper
    create    app/helpers/notes_helper.rb
    invoke    test_unit
    create      test/unit/helpers/notes_helper_test.rb
    invoke  assets
    invoke    coffee
    create      app/assets/javascripts/notes.js.coffee
    invoke    scss
    create      app/assets/stylesheets/notes.css.scss


This creates the controller and associated HTML files, but we'll need to go back and customize the details how it functions.

## Brief Introduction to REST    
Rails has adopted the REST HTTP protocol to describe how a Rails application interacts with database entries. REST stands for __Re__presention __S__tate__ __T__ransform. REST is a systematic way telling web applicaitons how/when to create, read, update, delete objects. HTTP stands for __H__yper __T__ext __T__ransfer __P__rotocol, and it's how your internet browswer communicates with wep applicaiton. Don't worry about HTTP, we'll focus on REST.

There are 2 ways to make a route (URL endpoints) "RESTful", implicit and explicit.

The implicit way is to use, ````resources :tasks````. IE, 
  
    # EXAMPLE - do not use 
    # routes.rb

    OpenCamp::Application.routes.draw do 
      resources :tasks
    end

Or the explicit way, 

    # EXAMPLE - do not use 
    # routes.rb

    OpenCamp::Application.routes.draw do 
      get    'tasks/index'     => 'tasks#index'
      get    'tasks/:id'       => 'tasks#show'
      get    'tasks/:id/edit'  => 'tasks#edit'
      get    'tasks/new'       => 'tasks#new'
      post   'tasks'           => 'tasks#create'
      put    'tasks/:id'       => 'tasks#update'
      delete 'tasks/:id'       => 'tasks#delete'
    end


### routes.rb changes - [Git Diff](https://github.com/railsmn/open_camp/commit/0a8b1967734d780ffa1f069b4860f2be014bcb22)  
All that said, let's use the 1 line ````resources```` command to make the ````notes```` URL RESTful.

    # ---------
    # COPY & PASTE this into routes.rb
    # ---------

    # routes.rb

    OpenCamp::Application.routes.draw do
      resources :notes
      resources :tasks
    end


### Index action - [Git Diff](https://github.com/railsmn/open_camp/commit/a7764e84e10377749495cc327cb6b473999475e8)    
Let's create a URL that shows a list of all the ````notes````.

#### Controller
Pull all ````notes```` from the database, and store them in a Ruby instance variable, ````@notes````. This will be passed to the HTML view file.

    # notes_controllerb

    class NotesController < ApplicationController

      def index
        @notes = Note.all
      end

      # ... other controller actions ...

    end

#### View
Now open ````open_camp/app/views/notes/index.html.erb````, and copy in the HTML table to show a list of notes.
  
    <h1>Listing notes</h1>

    <table>
      <tr>
        <th>Title</th>
        <th>Body</th>
        <th></th>
        <th></th>
        <th></th>
      </tr>

    <% @notes.each do |note| %>
      <tr>
        <td><%= note.title %></td>
        <td><%= note.body %></td>
        <td></td>
        <td></td>
        <td></td>
      </tr>
    <% end %>
    </table>



### Show action - [Git Diff 1](https://github.com/railsmn/open_camp/commit/6f40de60ab32f5104412373e00a28fcd70502beb) - [Git Diff 2](https://github.com/railsmn/open_camp/commit/38a8f527619aca576fd921893a58c263ae6bbefd) - [Git Diff 3](https://github.com/railsmn/open_camp/commit/7fbab67c5fbdf5d42c84069106eab940fa08faba)  
Now we want to look craete a URL and HTML page to view a single ```Note```.

#### Controller
The ````show```` URL will look like this,

    www.example.com/notes/1

The RESTful URL structure that Rails uses puts the ````1```` into a HTTP param called ````id````. We can access this parameter by calling this in the controller, ````params[:id]````. We can use this to query for a Note where the ````id```` is equal the integer.

    # notes_controller.rb

    class NotesController < ApplicationController

      def index
        @notes = Note.all
      end

      def show
        @note = Note.find(params[:id])
      end

      # ... other controller actions ...

    end


#### View 
    <p id="notice"><%= notice %></p>

    <p>
      <b>Title:</b>
      <%= @note.title %>
    </p>

    <p>
      <b>Body:</b>
      <%= @note.body %>
    </p>

    <%= link_to 'Back', notes_path %>

Note that we added the ````link_to```` ruby method. This creates a __dynamic__ link from the __show__ HTML page to the __index__ page.

[Let's add a link on the __index__ page to each show page](https://github.com/railsmn/open_camp/commit/38a8f527619aca576fd921893a58c263ae6bbefd), 

    <%= link_to 'Show', note_path(note) %> 



### New action - [Git Diff 1](https://github.com/railsmn/open_camp/commit/81b069d3da0f316a755cdc3b0d8202a9f5534cc6) - [Git Diff 2](https://github.com/railsmn/open_camp/commit/bec9090124622c0348e1fb2608970cf580881fb3) - [Git Diff 3](https://github.com/railsmn/open_camp/commit/f82cb95d749da7362e420e49908ea5d25f2c8833)  
Now let's make a way for us to create notes, 

#### Controller
We'll add 2 methods,  ````new````  and  ````create````. 

    # notes_controller.rb

    class NotesController < ApplicationController

      def index
        @notes = Note.all
      end

      def show
        @note = Note.find(params[:id])
      end

      def new
        @note = Note.new
      end

      def create 
        @note = Note.new(params[:note])

        if @note.save
          redirect_to @note, notice: 'Note was successfully created.'
        else
          render action: "new"
        end
      end

      # ... other controller actions ...

    end    


#### Views
We'll create a form, and place it in something called a ````partial````. Partials are small reusable bits of HTML of code. It will make more sense as we use it.

new.html.erb

    <h1>New note</h1>

    <%= render 'form' %>

    <%= link_to 'Back', notes_path %>
    
_form.html.erb

    <%= form_for(@note) do |f| %>
      <% if @note.errors.any? %>
        <div id="error_explanation">
          <h2><%= pluralize(@note.errors.count, "error") %> prohibited this task from being saved:</h2>

          <ul>
          <% @note.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
          <% end %>
          </ul>
        </div>
      <% end %>

      <div class="field">
        <%= f.label :title %><br />
        <%= f.text_field :title %>
      </div>
      <div class="field">
        <%= f.label :body %><br />
        <%= f.text_field :body %>
      </div>
      <div class="actions">
        <%= f.submit %>
      </div>
    <% end %>


### Update action - [Git Diff](https://github.com/railsmn/open_camp/commit/fbe6948aa16558bddeb3a41ee9c5be2ae5e0561a)
Great. We can create a note, but now we want edit the ````note````.

#### Controller
We'll load a note's attributes into the form, then get the attributes from the HTML form, and save them on form submission, 

    # notes_controller.rb

    class NotesController < ApplicationController

      # ... other controller actions ...

      def edit
        @note = Note.find(params[:id])
      end

      def update
        @note = Note.find(params[:id])

        if @note.update_attributes(params[:note])
          redirect_to @note, notice: 'Note was successfully updated.'
        else
          render action: "edit"
        end
      end

      # ... other controller actions ...

    end    

#### View

edit.htm.erb

    <h1>Editing note</h1>

    <%= render 'form' %>

    <%= link_to 'Show', @note %> |
    <%= link_to 'Back', notes_path %>

index.html.erb - added link to edit a note. 

    <%= link_to 'Edit', edit_note_path(note) %>

show.html.erb - added link to edit a note. 
    
    <%= link_to 'Edit', edit_note_path(note) %>    


### Delete action - [Git Diff](https://github.com/railsmn/open_camp/commit/7d9f6bc76314f9d85bd74a704536764402509e02)
#### Controller
We'll find the Note object, and destroy it,

    # notes_controller.rb

    class NotesController < ApplicationController

      # ... other controller actions ...

      def destroy
        @note = Note.find(params[:id])
        @note.destroy

        redirect_to notes_url
      end

    end    

#### View
There are no views since we redirect to the ````index``` page from the cotnroller action.





[Link to presentation slides](http://railsmn.github.io/convention-mvc-rest.html)

### 3 guiding principles of Rails and what they mean for web development

- Convention over Configuration
    - Ruby on Rails makes opionated decisions ('the Rails way') about a lot of common trivial decisions a web developer has to make to maximize developer productivity. Some of these conventions include how to name things in your code, where to put files, handling common security related issues, what template handler to use, how to compile 'assets' in production, etc
    - Creates established sets of 'best practices' that you can leverage in your application and makes it easy to understand other Rails applications
- MVC
    - Model, View, Controller (MVC) is a web development 'architecture' that specifies how the responsibilities of your code should be organized. This pattern makes it easy to understand what your code is doing and as a result easier to maintain. Model code represents data and its interaction with your databases in your application, View code represent the user interface (html with embedded Ruby code) and Controllers represent the business logic or 'the glue' between your Model code and View code
- REST
    - REST gives an easy to understand URL structure that corresponds to the resources (e.g. blog posts, users, etc) of your application which also creates another set of conventions that makes Rails development more seamless. REST is also the most accepted standard for which web applications can communicate with each other (e.g. your application pulling data from Twitter)

### Databases 

- What is a database
    - SQL
- How Rails interacts with databases 
    - What is an ORM?
    - ActiveRecord
- Different types of databases
    - Relational (MySQL, PostgreSQL, Oracle) vs non-relational (MongoDB, Redis, etc)



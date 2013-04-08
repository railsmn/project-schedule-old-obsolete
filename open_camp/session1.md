
## Welcome!  



## Purpose of building OpenCamp  

why rails.  

technical principles we will demonstrate.  

explain the difference between websites // web apps (applications).



## Logistics  

1. Setting up a Rails development env (if you have not installed Rails).
Getting Ruby on Rails setup the first time can be tricky. But we have a great solution that makes it easy, [railsmn-dev-box](https://github.com/railsmn/railsmn-dev-box). Checkout the README.md for install setups.

2. Text editor. 
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


3. We will be using the command line // terminal a lot.
Rails utilizes the unix command line // terminal commands. This may be confusing and/or frustrating at first, but we would like to challenge you to embrace the command line operations we walk through. It may be hard at first, but once you get comfortable with the command line operations, you'll feel like a super hero navigating the Rails universe. We promise!



## Session 1  

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

We need to change the default postgresql configuration. We need to change the ````username```` and ````encoding````.

We need to change the username to ````vagrant````. The ````rails-dev-box```` VM postgresql instance was setup with a ````vagrant```` user. [Here's where that username was specified](https://github.com/railsmn/railsmn-dev-box/blob/master/puppet/manifests/default.pp#L79).

    # config/database.yml

    # OLD_DEVELOPMENT_CONFIGURATION
    #
    development:
      adapter: postgresql
      encoding: unicode
      database: open_camp_development
      pool: 5
      username: open_camp
      password:
    
    # NEW_DEVELOPMENT_CONFIGURATION
    #
    development:
      adapter: postgresql
      encoding: unicode
      database: open_camp_development
      pool: 5
      username: vagrant
      password:

    # change username to vagrant for the __test__ and  __production__ dbs  
    

We need to change the encoding to ````SQL_ASCII````. Again, the ````rails-dev-box```` VM postgresql instance was setup with the default encoding as ````sql_ascii````. [Here's where that encoding was specified](https://github.com/railsmn/railsmn-dev-box/blob/master/puppet/modules/postgresql/templates/postgresql.conf.erb#L495).

    # config/database.yml

    # OLD_DEVELOPMENT_CONFIGURATION
    #
    development:
      adapter: postgresql
      encoding: unicode
      database: open_camp_development
      pool: 5
      username: vagrant
      password:
    
    # NEW_DEVELOPMENT_CONFIGURATION
    #
    development:
      adapter: postgresql
      encoding: SQL_ASCII
      database: open_camp_development
      pool: 5
      username: vagrant
      password:

    # change encoding to SQL_ASCII for the __test__ and  __production__ dbs  


### Create Databases  

To create the databases (development and test)  

    # make sure you are in the rails app, IE
    # 
    # vm -- /vagrant/open_camp
    #
    rake db:create


### Create a rails MVC component

We want to create Model, Controller, and View. This will allow Create/Read/Update/Delete stuff in the web browser and see it in the database. We'll use the ````rails```` executable, and use the rails' ```generate```` command.

    # executable   = rails
    # command      = generate
    # type         = scaffold
    # object       = Task
    # attributes   = name:string description:string
    #
    rails genearte scaffold Task name:string description:string

    # resulting output
    #
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


Let's update the database to hold ````task```` entries, 

    # run the database migration (update the db schema)
    rake db:migrate

    ==  CreateTasks: migrating ====================================================
    -- create_table(:tasks)
    NOTICE:  CREATE TABLE will create implicit sequence "tasks_id_seq" for serial column "tasks.id"
    NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "tasks_pkey" for table "tasks"
      -> 0.0033s
    ==  CreateTasks: migrated (0.0038s) ===========================================


Let's test it out in the browser
    
    # start the built in rails server
    rails server

    # go to 
    # 
    # http://localhost:3000/tasks


build non-scaffolded MVC object, Note.  
create the controller + views for new/show/edit/index.  
- content should be static.  
create the migration + migration.  
- Note object, subject, body
create partial form  
create the object and confirm via command line.  
redirect action.  
build index action and page.  
build show action and page.  
build edit action and pag (and update).  
build destroy action.  

  



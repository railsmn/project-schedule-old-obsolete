Session 2 - May 13th
=====

**IMPORTANT REMINDER**  
If you have not setup the [railsmn-dev-box](https://github.com/railsmn/railsmn-dev-box), please follow setup instructions on the [README page](https://github.com/railsmn/railsmn-dev-box). If you did not finish [Session 1 activities](https://github.com/railsmn/schedule/blob/master/open_camp/session1.md), you can pull down pull down the code we wrote during session 1. Here's how to do this via git commands,  

    # go to the folder where you've installed the railsmn-dev-box
    cd railsmn-dev-box

    # run this command to apply changes we made to the vagrant VM
    vagrant provision

    # ssh into the vagrant virtual machine
    vagrant ssh 

    # make sure to change directories to the VAGRANT folder
    cd /vagrant

    # clone the open_camp project
    git clone git://github.com/railsmn/open_camp.git
    
    # checkout the session1 changes
    git checkout session1_post



As always, please remember to be vocal during these events and feel more than welcome to ask questions. There will be many experienced Ruby on Rails developers glad to help!

## Introduction (30 minutes)
#### 1. Welcome and logistics
Enjoy pizza and beer and be sure to introduce yourself to other people learning Ruby on Rails!

While doing that...
  - Join the [RailsMN Google Group!](https://groups.google.com/forum/?fromgroups#!forum/railsmn)
  - Follow [@RailsMN on Twitter!](http://www.twitter.com/railsmn)

Per feedback, [Derek Rockwell](http://www.twitter.com/derekrockwell) and [Weston Platter](http://www.twitter.com/westonplatter) will introduce themselves.

Thanks to our sponsors, Google for Entrepreneurs and CoCo Minneapolis for the awesome space, food and refreshments!

#### 2. [Session 1](https://github.com/railsmn/schedule/blob/master/open_camp/session1.md) - Recap
Last month we had our first event at CoCo! The goal of the event was to orient ourselves with the structure of sessions and make the initial strides towards building OpenCamp. Specifically we:

- Set up our virtual machine
- Created the first OpenCamp code
  - Created a task scaffold with models, views and controllers created for us
  - (Potentially) Created a note object following the scaffold pattern
- Discussed the importance of MVC, REST and databases within Ruby on Rails

[Here is a link to the codebase to be expected at the end of Session 1](https://github.com/railsmn/open_camp_session_1/tree/master/finish). If you haven't gotten yet to that point on your own, pull a mentor aside to figure out how to pull down the codebase we will be starting with today.

#### 3. Update your Virtual Machine!
We made some changes to our Virtual Machine image and fixed some glitches that will make your development experience smoother going forward (e.g. Ruby 2.0, default ssh directory, Rails installed (woopsy)). To make sure you are up to date with the correct environment, follow these steps:

1. Open a terminal and navigate to the directory where your existing virtual machine is stored, probably in a railsmn-dev-box folder. The below example is for me, your directory location may be different.
  
  ```
  cd ~/projects/railsmn/railsmn-dev-box
  ```

2. Now we need to pull down the latest changes we added in this directory from GitHub. 

  ```
  git pull origin master
  ```

3. With the new configuration we need to rebuild our box. Don't worry, this won't delete the work you've done. This process will take a few minutes depending on your machine.

  ```
  vagrant destroy
  vagrant up
  ```

4. Now to connect back into our virtual machine we may SSH again as expected from last session. Note you no longer need to change to the /vagrant directory, as we set that to be the default behavior.

  ```
    vagrant ssh
  ```  

#### 4. Session 2 Overview
We made some slight changes to the structure of future sessions as a result of your feedback. 

Our introductory time will be used to summarize and go over what we've done with OpenCamp thus far.

Our hack time is now going to be more compartmentalized! We'll give quick instructions for each subsection of the plan and bring the group back for a quick discussion about the previous step(s).

Our advanced topics presentations will be slightly different. We'll now do 3 different topics, 10 minutes each in a lightning talk format. Mentors and members are encouraged to participate by volunteering to present on a relevant Rails/Web Development topic or best practice.

The rest of this evening will be comprised of:

- Adding user authentication
- Creating user tests
- Lightning talks 
  - Test Driven Development
  - Capybara
  - Git

## Hack Time (90 minutes)

#### 1. User authentication
1. Install the Devise Gem

  Explanation (we should probably explain the concept of gems and link to some resources, follow suit with any other new concepts below). For more information on how to use Devise outside of what we are doing today, [visit the Devise GitHub page](https://github.com/plataformatec/devise)

  To start, add the following line to your Gemfile outside of any 'group' blocks to bring Devise into Rails
  
  ```
  gem 'devise'
  ```

  Once you add Devise to your Gemfile, you need to use Bundler to pull in the code to your application, in your terminal run:

  ```
  bundle install
  ```

  Now we need to run some code that comes with Devise to add the appropriate configurations (config/initializers/devise.rb) and your new User model (app/models/user.rb), and the necessary migrations. To do that, run the following in your terminal:

  ```
  rails generate devise:install
  rails generate devise user
  rake db:migrate
  ```

2. Add Devise to Controllers

  Now with Devise installed we can make it so a User needs to be authenticated before creating a task! To do so, open up ````app/controllers/tasks_controller.rb```` and add the following filter underneath the class declaration

  ```
  before_filter :authenticate_user!
  ```

  This is our first experience with 'filters' in Rails. Filters are methods that are run before or after our controller methods. In this case, we are calling the Devise ````'authenticate_user!'```` method before we can create, edit or destroy tasks. For more information on filters, visit [the Ruby on Rails filter guide](http://guides.rubyonrails.org/action_controller_overview.html#filters)

  To play with this, you can now visit http://localhost:3000/tasks and you will see a login screen. You can now sign up and then be redirected to the tasks index.
  

#### 2. Adding tasks to users
1. Update models

  Now that we require authentication to create or change tasks, it would be nice if we could directly associate tasks with a specific user, instead of having all tasks belong to all users. The association between users and tasks is easy to create in Rails!

  The first step of associating things in Rails is to let the database know about the unique ID that will relate the tasks with their users. The convention in Rails is to follow the <model>_id naming pattern. To easily update the database, we can run the following migration.

  ```
  rails generate migration AddUserToTasks user_id:integer
  rake db:migrate
  ```

  For more information on migrations, check out [the Ruby on Rails migration guide](http://guides.rubyonrails.org/migrations.html)

  Now we need to update our ````user.rb```` and ````task.rb```` models in order to work with this new relationship. Open up ````app/models/user.rb```` and add the following in the class definition:
  
  ```
  has_many :tasks
  ```

  Open up ````app/models/task.rb```` and add the following in the class definition:

  ```
  belongs_to :user
  ```

  At this point, restart your Rails server for the model changes to take effect. Now you can run commands like ````user.tasks```` to get all of the users tasks or ````task.user```` to find the owner of a task. For more information on model associations, check out [the Rails guide on Active Record associations](http://guides.rubyonrails.org/association_basics.html)

2. Update controllers

  Now that we've set the ground work, we need to tell our controller to save deal with tasks in context of our currently logged in user.

  1. Update the index method     

      Our first change is to make sure our users are only seeing the tasks that they own. Open up ````app/controllers/tasks_controller.rb``` and change this line within the 'index' method:

      ```
      @tasks = Task.all
      ```

      to be

      ```
      @tasks = current_user.tasks
      ```

      ````current_user.tasks```` is pulling the currently logged in user from Devise and using our model methods that we created in the prior step to make sure we only pull up the relevant tasks.

  2. Update the create method

      Next we need to make sure that when we save tasks, we also save the relationship to the logged in user. In the 'create' method, underneath the following line:

      ```
      @task = Task.new(params[:task])
      ```

      add this

      ```
      @task.user = current_user
      ```

  3. (Optional) update other methods

      Those are the two methods that we need to change for functionality sake. However, if we don't modify the other methods it may be possible to change other users tasks by changing our URLs. Feel free to update the other methods using Devise's ````current_user````

#### 3. User testing

  1. Do something

  Explanation

  ```
  Code
  ```

  2. Do something

  Explanation

  ```
  Code
  ```


## Lightning Talks (30 minutes)
**Test Driven Development:** It is cool and important. We'll talk about why.

**Capybara:** Also cool, helps facilitate Test Driven Development and do 'full-stack' testing.

**Git and Version Control:** You've been using Git already! We'll explain the history of Git and some tips and tricks.

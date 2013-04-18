---
layout: post
title: "Rails 3 authentication for n00bs"
date: 2013-03-23 18:01
comments: true
categories: _rails_ authentication refactoring urlhelper validation
---
<h3><em>Progressing my ToDoList app - making it work, making it right</em></h3>
<img src="http://ei-lene.github.com/images/2013_03_23/password_authentication.png">
<p style="font-size: 70%; font-weight: normal">
Source: <a href="http://euroscientist.com/2012/03/picking-a-perfect-password/">On picking a perfect password by Euroscientist</a>
</p>

Since I have a sieve-like memory, I really need to take detailed notes and practice practice in order to remember how to do things. Hence, I am trying to apply what we’ve learned through lectures in my ToDoList ‘practice’ app, especially on areas that I haven’t had a chance to personally code in our current group ‘Hand Raise’ project. 

At the end of my <a href="http://ei-lene.github.com/blog/2013/03/06/my-top-4-rails-tricks-in-my-first-rails-app/">previous blog post on my ToDoList app</a>, my next steps were to:<br>
1)   Change the navigation links so they are conditional<br>
2)   Add authorization for Users to create, edit and delete tasks

But before authorizing users, we first need to authenticate users, and this will be the main body of my post (<a href="#work">making it work</a>). The remainder of my post (<a href="#right">making it right</a>) is about refactoring, conditionally displaying links and some validation pitfalls that tripped me up.

<!--more-->

<strong><a name="work">Making it work - Rails 3 Authentication in 4 parts: </a></strong><br>
<strong><em>1) Leveraging Rails 3 functionality</em></strong><br>
Rails 3 includes a <a href="http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html - method-i-has_secure_password">has_secure_password class method</a> which simplifies authentication for the beginner and/or the gem-shy.<br>
i) Generate a User model which includes a <strong>password_digest</strong> string field, which is required to leverage the <strong>has_secure_password </strong> functionality<br>
ii)Add <strong>has_secure_password </strong> in the User model<br>
iii) Include (or uncomment) the <strong>bcrypt-ruby </strong> gem in the Gemfile

<strong><em>2) Creating logins and sessions</em></strong><br>
The act of logging in is the start of a new user session, which is a virtual model, which means (per my interpretation), that there are specific rules that govern each session, but that there is no need for an actual model, because nothing is persisted into the database.<br>
i) Generate a sessions controller: ```rails g controller sessions```<br>
ii) Create a login form in view. Note that you should use the <strong>form_tag</strong> helper instead of the form_for helper because there is no sessions model<br>
iii)In the <em>Sessions controller</em>, write a <strong>create</strong> method, which says:
if a <em>user exists</em> and the <em>password can be authenticated</em> (note that ```.authenticate``` is a method given by ```has_secure_password```), then <em>set the user’s id</em> as <em> the user_id of the session</em>.
{% codeblock SessionsController lang:ruby %}
def create
  user = User.find_by_email(params[:email])
  if user && user.authenticate(params[:password])
    session[:user_id] = user.id 
    redirect_to root_url, notice:"Logged in!"
  else
    flash.now.alert = "Email or password is invalid"
  end
end
{% endcodeblock %}
iv) Logging out would simply entail setting the session’s user_id to nil: ```session[:user_id] = nil```<br>
v) Logging out in views is a bit tricky:
``` ruby
<%= link_to "Log out", session_path(“current user”), method: "delete" %>
```
--> even though the session ```destroy``` method does not accept any arguments, the ```session_path``` expects an argument, so just put any argument in<br>
--> need to include <strong>method: “delete”</strong> (note that ```method: :destroy``` doesn’t work)

<strong><em>3) Allowing the application remember that a user logged in</em></strong><br>
Create an instance variable to allow the app to remember who the current user is.<br>
i) In <em>Application Controller</em> (so it is set app-wide), create a <em>private method</em> to <em>store the session’s user_id</em> as the <em>current_user</em>, if session user_id exists.
{% codeblock ApplicationController lang:ruby %}
def current_user
  @current_user ||= User.find_by_id(session[:user_id]) if session[:user_id]
end
helper_method :current_user
{% endcodeblock %}

--> add a ```helper_method``` to allow the method to be accessible to all the Views

<strong><em>4) Allowing the application to test if a user is logged in</em></strong><br>
i) In <em>ApplicationController</em>, create a <em>private method</em> <strong>logged_in?</strong> or <strong>authorize</strong> (or anything that is sensibly-named) that tests if a user is logged in.<br>
Either:
``` ruby
true if current_user
```
Or:
``` ruby
redirect_to new_session_path if current_user.nil?
```
ii) In each <em>relevant controller</em> (in this case, Lists, Tasks, Users), create a <strong>before_filter</strong> which runs on specific actions with options, e.g.
``` ruby
before_filter logged_in?, only: [:edit, :destroy]
```
or: 
``` ruby
before_filter logged_in?, except: [:show, :index]
```
<strong><a name="right">Making it right - refactoring authentication, conditionally displaying links, validation pitfalls</a></strong><br>
<strong><em>1) Refactoring authentication</em></strong><br>
i) Prettifying routes<br>
To change routes from users/new, sessions/new etc. to signup and login, we need to change the routes and then update the views.<br>
Adding the following routes to the routes file...
``` ruby
get 'signup'=> 'users#new'
get 'login' => 'sessions#new'
post 'login' => 'sessions#create'
get 'logout'=> 'sessions#destroy'
```
...enables us to refactor our code in Views from...
``` ruby
<%= link_to "Sign up", new_user_path %>
<%= link_to "Log in", new_session_path %>
<%= link_to "Log out", session_path(current_user), method: "delete" %>
```
to...
``` ruby
<%= link_to "Sign up", signup_path %>
<%= link_to "Log in", login_path %>
<%= link_to "Log out", logout_path %>
``` 

ii) Further abstracting methods<br>
We can encapsulate the logic of setting session user_ids in private methods in the ApplicationController:
{% codeblock ApplicationController lang:ruby %}
def login(user)
  session[:user_id] = user.id
end

def logout
  session[:user_id] = nil
end
{% endcodeblock %}
We can then refactor the SessionsController to use these methods.

<strong><em>2) Conditionally displaying links</em></strong><br>
I wanted to hide the navigation links for a page if a user is already on that page. The <a href="http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html">Rails UrlHelper</a> from the ActionView::Helpers were very useful in this regard.

Methods such as ```link_to_if```, ```link_to_unless``` and ```link_to_unless_current``` provide a lot of options for toggling links on and off.

But to completely hide a link, when a user is on a specific page, I used the ```current_page?``` method as follows:
{% codeblock ApplicationController lang:ruby %}
<% unless current_page?(root_path) %>
  <li><%= link_to "Home", root_path %></li>
<% end %>
{% endcodeblock %}

<strong><em>3) Validation pitfalls</em></strong><br>
In this set of iterations, I have also incorporated additional <a href="http://ei-lene.github.com/blog/2013/03/11/front-end-form-validation-my-top-3-jquery-apis">front-end</a> and back-end validations that we learned a couple of weeks ago.

One problem I encountered was with password validation, where I initially applied 
{% codeblock user.rb lang:ruby %}
validates :password, :length => {:minimum => 6 }
{% endcodeblock %}

This broke my ability to assign a user to a task when creating and adding a task to a list. And that took my AGES to debug. Finally, I realized that the user wasn’t saving because the password validation was failing.  I got around it by only validating the password on creation:
{% codeblock user.rb lang:ruby %}
validates :password, :length => {:minimum => 6 }, :on => :create
{% endcodeblock %}

Another issue I grappled with was the case-sensitivity of emails. I came across ```:case_sensitive => false```, but <a href="http://pivotallabs.com/standup-5-21-2010-validates-uniqueness-of-and-case-sensitivity/">some</a> (admittedly dated) <a href="http://techblog.floorplanner.com/post/20528527222/case-insensitive-validates-uniqueness-of-slowness">blogs</a> have suggested that it slows down applications, so I have downcased the emails when creating a user, and also downcased the email input when creating a session.
{% codeblock UsersController lang:ruby %}
def create
  @user = User.new(params[:user])
  @user.email = @user.email.downcase
  @user.save
end
{% endcodeblock %}
{% codeblock SessionsController lang:ruby %}
def create
  user = User.find_by_email(params[:email].downcase)
  # other code omitted
end
{% endcodeblock %}
<p style="font-size: 70%; font-weight: normal">
Resources:<br>
1) <a href="http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html - method-i-has_secure_password">ActiveModel at http://api.rubyonrails.org/</a>
<br>
2) Authentication from scratch prior to Rails 3: <a href="http://railscasts.com/episodes/250-authentication-from-scratch">RailsCasts</a> / <a href="http://asciicasts.com/episodes/250-authentication-from-scratch">ASCIIcasts</a><br>
3) <a href="http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html">ActionView at http://api.rubyonrails.org/</a>

</p>
---
layout: post
title:  "Rails Sessions, Flash, and Cookies"
date:   2017-04-18 08:00:00 -0400
categories: Rails
---
# Cookies in Rails

Cookies are a developers tool to take stateless HTTP requests and give them state. In other words, they allow us differentiate between a `GET` request from one browser and a `GET` request from another. They let us do this by telling each browser to send some data within the body of their request in the form of a `params` associative array (_aka a **hash** in ruby_.) We can then use this data, which we set at some point after the user visits our site (e.g. on login), to figure out which user is which.

## Sessions vs. Cookies
Rails gives us some convenient, built in ways to manage cookies. It comes standard with 3 methods: `session`, `flash`, and `cookies`. Two of them (`session` and `cookies`) work very similarly, with the exception that the `session` method encrypts the data so that it can't be edited by the user.  

You can see this difference if you look at your cookies and sessions on a rails server:
{% highlight sh %}
$: rails new demo_app

$: rails g scaffold post title body published

{% endhighlight %}
Then, inside the post_controller.rb file, add these lines in the `create` action.
{% highlight ruby %}
def create
  cookies[:created_post_at] = Date.today
  session[:created_post_at] = Date.today
  ...
end
{% endhighlight %}
Then back in your console:
{% highlight sh %}
$: rake db:migrate

$: rails s

{% endhighlight %}
Now, we can go visit `localhost:3000/posts/new`, and fill out our form. Afterwards, we should see:  
![Post Form](/assets/images/rails-sessions-flash-and-cookies/post-form.png)
![Created Post](/assets/images/rails-sessions-flash-and-cookies/created-post.png)

We can now look at our cookies and find:
![Normal Cookie](/assets/images/rails-sessions-flash-and-cookies/normal-cookie.png)

![Session Cookie](/assets/images/rails-sessions-flash-and-cookies/session-cookie.png)

As you can see, the `cookies` method created a cookie with the name that we passed in as a key, and the value that we set. The `session` method, however, created a cookie with the name of our app followed by `_session` and a key that looks like a random string. This string is an encrypted hash that we can access with the `session` method. Inside this hash, the key `created_post_at` will have the value `2017-04-19`.

_Note that, although the **session** method by default persists data as a cookie, there are other ways that you can use it that will not be covered in this post._

## The Flash
Our third method for persisting data is the `flash` method. The `flash` method acts as though it is the `session` method, with the exception that the key-value pair only persists for 1 request.

We can demonstrate this by adding the following code to our controller in the `show` method:  
{% highlight ruby %}
flash[:display_this] = "You just visited the show page for #{@post.title}"
{% endhighlight %}  
and this code in our index view:  
{% highlight erb %}
<% if flash[:display_this].present? %>
  <pre><%= flash[:display_this] %></pre>
<% end %>
{% endhighlight %}

Starting from our index page (`localhost:3000/posts') we can delete all our cookies and navigate to the show page.  
![Index Original](/assets/images/rails-sessions-flash-and-cookies/index-original.png)
![No Cookies](/assets/images/rails-sessions-flash-and-cookies/no-cookies.png)

We can then navigate to the show page, and then back to the index page.  
![Show Page](/assets/images/rails-sessions-flash-and-cookies/show-page.png)
![Index Affected](/assets/images/rails-sessions-flash-and-cookies/index-affected.png)

Now, if we look at our cookies we can see that there is only the session cookie.
![Flash Cookie](/assets/images/rails-sessions-flash-and-cookies/flash-cookie.png)

At first glance, this will make the `flash` method seem like it does the same thing as the `session` method. However, if you refresh your page, you'll see that the message is gone!

The `flash` method is also accessible through the `redirect_to` method. By using the syntax:
{% highlight ruby %}
redirect_to posts_path, flash: { display_this: "You just visited the show page for #{@post.title}" }
{% endhighlight %}
you can prevent the session cookie from being changed until you redirect. You can also make a the `flash` method accessible in the current action by using:
{% highlight ruby %}
def update
  if @post.update( posts_params )
    ...
  else
    flash.now[ display_this: "There was a problem updating the #{@post.title} post"]
    render :edit
  end
end
{% endhighlight %}
or you can make sure that the flash key-value pairs aren't deleted from your session hash by using:
{% highlight ruby %}
flash.keep # persists all values for the flash method

flash.keep[ :display_this ] # persists only the :display_this key-value pair
{% endhighlight %}

The `flash` method also includes some convenient shortcuts for quickly assigning and accessing values of the keys `:notice` and `:alert`:
{% highlight ruby %}
flash.alert = "You must be logged in"
flash.notice = "Post successfully created"

redirect_to root_url, notice: "You have successfully logged out."
redirect_to root_url, alert: "You're stuck here!"
{% endhighlight %}

For more information on the `cookie` method or other ways of using the `session` method, check out these sites:
 + Session:
   + [Guides.RubyonRails](http://guides.rubyonrails.org/action_controller_overview.html#session)
   + [API.RubyonRails](http://api.rubyonrails.org/classes/ActionDispatch/Session/CookieStore.html)
 + Cookie:
   + [Guides.RubyonRails](http://guides.rubyonrails.org/action_controller_overview.html#cookies)
   + [API.RubyonRails](http://api.rubyonrails.org/classes/ActionDispatch/Cookies.html)

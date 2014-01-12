---
layout: post
title: "Mass assignment X Gravity"
description: ""
category: rails
tags: []
---

#What is Mass Assignment?
Mass assignment is using a ruby-esque shortcut to interact with models:

This is NOT mass assignment.
{% highlight ruby %}
def create
  u = User.new
  u.first_name = params[:user][:firstname]
  u.last_name = params[:user][:lastname]
  if u.save
    redirect_to :index, flash: { success: "Created!" }
  else
    render :action => 'new'
  end
end{% endhighlight %}

Each param is extracted from the params hash and assigned explicitly. Any params that are not assigned are thrown on the floor.

This IS mass assignment.

{% highlight ruby %}
def create
  u = User.new(params[:user])
  if u.save
    redirect_to :index, flash: { success: "Created!" }
  else
    render :action => 'new'
  end
end{% endhighlight %}
The programmer assumes that the params include all the data necessary, and _only_ the data necessary. All params in the hash are used to create the new user record.

###Why is mass assignment a problem?
The problem with mass assignment is that if you have a sensitive tag in your model, for example user_type (which could be set to admin), a malicious user can add `user[:user_type] = 'admin'` to your params hash. Your controller will unwittingly include it in the save command, creating a new admin account for the  malicious user.

###Is there any way to prevent this diabolical schema scheme?
There is! With Rails 4 comes **strong params**.
Strong params live in the controller and tell it explicitly what params can be trusted.
{% highlight ruby %}
def create
  u = User.new(user_params)
  if u.save
    redirect_to :index, flash: { success: "Created!" }
  else
    render :action => 'new'
  end
end
...
def user_params
  params.require(:user).permit(:name, :email, :password, :password_confirmation)
end{% endhighlight %}
The only parameters our controller will send to the model are those specified on our white-list. If rails detects a user attempting to access a param that is not on the white-list, said user had better be prepared for _grave consequences_. Their session will be **deleted**, their attempt will be **logged**, and Rails will email their mother.

Awesome.
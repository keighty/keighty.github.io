---
layout: post
title: "A Flash of Understanding"
description: ""
category: rails
tags: []
---

My page wouldn't flash the user.

Trundling along the [Hartl railstutorial](http://ruby.railstutorial.org/chapters/updating-showing-and-deleting-users?version=4.0#top), I needed to write some logic to allow a user to update their information.

As a [loyal TDD-er](http://www.katieleonard.ca/tdd/testing/2013/06/14/TDD-is-the-way-to-be/), I wrote a test to check that users see a success message if they are able to update their infomation:

{% highlight ruby %}
describe "updating with valid information" do
  let(:new_name) { "New Name" }
  let(:new_email) { "new@example.com" }
  before do
    fill_in "Name", with: new_name
    fill_in "Email", with: new_email
    fill_in "Password", with: user.password
    fill_in "Confirm Password", with: user.password
    click_button "Save changes"
  end
  it { should have_selector('div.alert.alert-success') }
end
{% endhighlight %}

As expected, the tests should fail until I am able to log in.

Since the Rails4 user scaffold comes with some nifty built in methods, I thought it would be easy to adapt them for my own ends:

{% highlight ruby linenum %}
# PATCH/PUT /users/1
# PATCH/PUT /users/1.json
def update
  respond_to do |format|
    if @user.update(user_params)
      format.html { redirect_to @user, notice: 'Profile updated.' }
      format.json { head :no_content }
    else
      format.html { render action: 'edit' }
      format.json { render json: @user.errors, status: :unprocessable_entity }
    end
  end
end{% endhighlight %}

I modify the line 5 of the user controller to change the "notice" message to a "success" message:
{% highlight ruby %}
format.html { redirect_to @user, success: "Profile updated" }{% endhighlight %}

Seems reasonable, but my test for the flash message is still failing. Why won't my page flash the user?!

Quick as a flash, I asked [Dash](https://itunes.apple.com/us/app/dash-docs-snippets/id458034879?mt=12) about FlashHash.

Lo, and behold! Notice and alert are built-in flash tags, but success is nowhere to be found. How can I express the satisfaction of `SUCCESS!!` with a tag as mundane as notice:?

Thanks to [duck-typing](http://en.wikipedia.org/wiki/Duck_typing) (if it walks like a duck and quacks like a duck, it is probably a hash.. right?) we can add our spiffy `success:` tag to flash, and share the appropriate level of enthusiasm with our users:

{% highlight ruby %}
format.html { redirect_to @user, flash: { success: "Profile updated" }}{% endhighlight %}

All tests are green, and today I learned that flash is

Awesome
---
layout: post
title: "debugging techniques"
date: 2015-02-02
comments: true
categories: [rails, toolbox]
---

I am working on upgrading an application from Rails 3.0 to Rails 3.2. While the numbers tell you it is a minor version change, there are definitely some major challenges. Digging through Rails and gem internals has forced me to add new strategies to my debugging toolbox, and here are a few of the essentials...<!--more-->

### [Zeus](https://github.com/burke/zeus)
Zeus is a gem that preloads your Rails app and keeps it loaded, making test driven development fast. Just type `zeus start` in one console window, and any of the listed commands in another console window.

{% img /images/zeus_start.png %}

{% codeblock lang:bash %}
$ zeus test test/models/user_test.rb
Run options: --seed 32298

# Running:

.

Finished in 0.045991s, 21.7434 runs/s, 21.7434 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
{% endcodeblock %}

Run a specific test in a file by passing a regex argument:

{% codeblock lang:bash  %}
$ zeus test test/models/user_test.rb -n "/test_user_description_has_full_name/"
Run options: -n /test_user_description_has_full_name/ --seed 17927

# Running:

.

Finished in 0.044550s, 22.4467 runs/s, 22.4467 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
{% endcodeblock %}

### [Pry](https://github.com/pry/pry)
The Pry gem allows you to set break points and open an interactive debug session at any place in your code using `binding.pry`. A great way to debug a failing test is to place a pry at the top of the test and check your setup:

{% codeblock lang:ruby Pry into a test %}
require 'test_helper'

class UserTest < ActiveSupport::TestCase
  def test_user_description_has_full_name
    user = User.create(first_name: "hello", last_name: "kitty", email: "kitty@example.com")

    binding.pry

    assert_equal "hello kitty", user.full_name
  end

end
{% endcodeblock %}

{% codeblock lang:bash %}
$ zeus test test/models/user_test.rb -n "/test_user_description_has_full_name/"

Run options: -n /test_user_description_has_full_name/ --seed 21519

# Running:

From: /Users/keighty/api_playground/test/models/user_test.rb @ line 7 UserTest#test_user_description_has_full_name:

     4: def test_user_description_has_full_name
     5:   user = User.create(first_name: "hello", last_name: "kitty", email: "kitty@example.com")
     6:
 =>  7:   binding.pry
     8:
     9:   assert_equal "hello kitty", user.full_name
    10: end

[1] pry(#<UserTest>)> user

=> #<User:0x007fa054180618
 id: 980190963,
 first_name: "hello",
 last_name: "kitty",
 email: "kitty@example.com",
 created_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00,
 updated_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00>
{% endcodeblock %}

Your pry is a debug console with more than a few extra features. Here are my favourites:

* `ls object` to see all the methods available on the object:

{% codeblock lang:bash %}
[3] pry(#<UserTest>)> ls user

ActiveRecord::Core#methods:
  <=>                 eql?                          hash          readonly!
  ==                  freeze                        init_with     readonly?
  connection_handler  frozen?                       inspect       set_transaction_state
  encode_with         has_transactional_callbacks?  pretty_print  slice
ActiveRecord::Persistence#methods:
  becomes     delete      increment!   toggle!           update_attributes
  becomes!    destroy!    new_record?  update            update_attributes!
  decrement   destroyed?  persisted?   update!           update_column
  decrement!  increment   toggle       update_attribute  update_columns
ActiveRecord::Scoping#methods:
  initialize_internals_callback  populate_with_current_scope_attributes
...
{% endcodeblock %}

* `whereami 10` to show 10 lines above and below your breakpoint:

{% codeblock lang:bash %}
[5] pry(#<UserTest>)> whereami 10

From: /Users/keighty/api_playground/test/models/user_test.rb @ line 7 UserTest#test_user_description_has_full_name:

     1: require 'test_helper'
     2:
     3: class UserTest < ActiveSupport::TestCase
     4:   def test_user_description_has_full_name
     5:     user = User.create(first_name: "hello", last_name: "kitty", email: "kitty@example.com")
     6:
 =>  7:     binding.pry
     8:
     9:     assert_equal "hello kitty", user.full_name
    10:   end
    11:
    12: end
{% endcodeblock %}

* `cd object.method` to navigate the object stack as if it were a folder structure (and `ls` to view properites):

{% codeblock lang:bash %}
[1] pry(#<UserTest>)> cd user.full_name

[2] pry("hello kitty"):1> ls

Comparable#methods: <  <=  >  >=  between?
JSON::Ext::Generator::GeneratorMethods::String#methods:
  to_json_raw  to_json_raw_object  to_json_without_active_support_encoder
String#methods:
  %                  downcase        lstrip!           strip_heredoc
  *                  downcase!       match             sub
  +                  dump            mb_chars          sub!
  <<                 each_byte       next              succ
  <=>                each_char       next!             succ!
  ==                 each_codepoint  not_ascii_only?   sum
  ===                each_line       oct               swapcase
  =~                 empty?          ord               swapcase!
{% endcodeblock %}

These are the features I use most often, but checkout the [documentation](http://pryrepl.org/) for more.

### Method Method Method

Method name collisions can happen in a sprawling legacy codebase, so determining the source is important for debugging. The Ruby language has anticipated this difficulty, and since methods are objects themselves, they created the [Method](http://www.ruby-doc.org/core-2.2.0/Method.html) module to access the method object. You can source, extract, unbind, and otherwise manipulate methods in a lot of ways:

{% raw %}
```
[1] pry(#<UserTest>)> method = user.method(:full_name)
=> #<Method: User#full_name>

[2] pry(#<UserTest>)> method.owner
=> User(id: integer, first_name: string, last_name: string, email: string, created_at: datetime, updated_at: datetime)

[3] pry(#<UserTest>)> method.source_location
=> ["/Users/keighty/api_playground/app/models/user.rb", 2]
```
{% endraw %}

### Logger
Soemtimes the Rails bug you are hunting turns up in the SQL behind your query. When you are in a pry console, the SQL is not displayed by default, but you can turn on query logging by creating an `ActiveRecord::Base.logger` object:

{% raw %}
```
# before the logger
[1] pry(#<UserTest>)> User.find(980190963)

=> #<User:0x007fa0556f4fd0
 id: 980190963,
 first_name: "hello",
 last_name: "kitty",
 email: "kitty@example.com",
 created_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00,
 updated_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00>

[2] pry(#<UserTest>)> ActiveRecord::Base.logger = Logger.new STDOUT;

[3] pry(#<UserTest>)> User.find(980190963)

D, [2015-02-07T09:28:34.892017 #10468] DEBUG -- :
User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT 1  [["id", 980190963]]

=> #<User:0x007fa05c788c10
 id: 980190963,
 first_name: "hello",
 last_name: "kitty",
 email: "kitty@example.com",
 created_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00,
 updated_at: Sat, 07 Feb 2015 16:52:21 UTC +00:00>
```
{% endraw %}

If you find that you are always creating a logger, add these lines to a `.pryrc` file in your home directory:

{% codeblock lang:bash  %}
if defined?(Rails) && Rails.env
  require 'logger'
  ActiveRecord::Base.logger = Logger.new(STDOUT)
  ActiveRecord::Base.clear_active_connections!
end%
{% endcodeblock %}

### Globals
If you are bug hunting deep inside a gem or rails internals, and are trying to pry into a module that is loaded many times before it hits the buggy line of code, a simple `binding.pry` will have you stepping through an impossible amount of setup. Try setting a global variable to `true` right before the buggy line, and `binding.pry` on that condition:

{% codeblock lang:bash %}
➜  api_playground  zeus test /Users/keighty/api_playground/test/models/user_test.rb -n "/test_user_description_has_full_name/"
Run options: -n /test_user_description_has_full_name/ --seed 28811

# Running:


From: /Users/keighty/api_playground/app/models/user.rb @ line 5 User#full_name:

    2: def full_name
    3:   $bedug = true
    4:   "#{first_name} #{last_name}"
    5:   binding.pry if $bedug
    6: end
{% endcodeblock %}

A global will maintain its state no matter how many times the code is loaded, so be sure to set the variable to false if you are stepping past the first break.

### Caller
Calling `caller` at a pry prompt will output the current execution stack:

{% raw %}
```
[1] pry(#<User>)> caller
=> ["/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:355:in `eval'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:355:in `evaluate_ruby'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:323:in `handle_line'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:243:in `block (2 levels) in eval'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:242:in `catch'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:242:in `block in eval'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:241:in `catch'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_instance.rb:241:in `eval'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:77:in `block in repl'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:67:in `loop'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:67:in `repl'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:38:in `block in start'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/input_lock.rb:61:in `call'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/input_lock.rb:61:in `__with_ownership'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/input_lock.rb:79:in `with_ownership'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:38:in `start'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/repl.rb:15:in `start'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/pry_class.rb:169:in `start'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/pry-0.10.1/lib/pry/core_extensions.rb:43:in `pry'",
 "/Users/keighty/api_playground/app/models/user.rb:5:in `full_name'",
 "/Users/keighty/api_playground/test/models/user_test.rb:6:in `test_user_description_has_full_name'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:108:in `block (3 levels) in run'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:206:in `capture_exceptions'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:105:in `block (2 levels) in run'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:258:in `time_it'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:104:in `block in run'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest.rb:327:in `on_signal'",
:
```
{% endraw %}

`caller` is a [Kernel method](http://www.ruby-doc.org/core-2.2.0/Kernel.html#method-i-caller). Pass a start line and a line limit to truncate the stack:

{% raw %}
```
[11] pry(#<User>)> caller(20, 5)
=> ["/Users/keighty/api_playground/app/models/user.rb:5:in `full_name'",
 "/Users/keighty/api_playground/test/models/user_test.rb:6:in `test_user_description_has_full_name'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:108:in `block (3 levels) in run'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:206:in `capture_exceptions'",
 "/usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/minitest-5.5.1/lib/minitest/test.rb:105:in `block (2 levels) in run'"]
```
{% endraw %}


The first 20 or so lines are usually from the pry session so they are easily ignored. I can see that the User.full_name method is being called from my test -- a trivial case, but it comes in very handy when debugging a deeper stack.

### Errors
When models have many association dependencies, satisfying the validations of many dependencies can be tricky without descriptive errors. In Rails 3.2, the error storage was changed from an [OrderedHash](http://www.rubydoc.info/docs/rails/3.0.0/ActiveModel/Errors) to a first class ruby object in [ActiveModel](http://www.rubydoc.info/docs/rails/3.2.8/ActiveModel/Errors). While the interface has changed, its value remains:

{% raw %}
```
[1] pry(main)> user = User.create
=> #<User:0x007fa054525318
 id: nil,
 first_name: nil,
 last_name: nil,
 email: nil,
 created_at: nil,
 updated_at: nil>

# Why does my object not have an id?

[2] pry(main)> user.errors
=> #<ActiveModel::Errors:0x007fa05451fda0
 @base=
  #<User:0x007fa054525318
   id: nil,
   first_name: nil,
   last_name: nil,
   email: nil,
   created_at: nil,
   updated_at: nil>,
 @messages={:first_name=>["can't be blank"], :last_name=>["can't be blank"]}>

[3] pry(main)> user.errors.full_messages
=> ["First name can't be blank", "Last name can't be blank"]

# oooooh... validations

```
{% endraw %}

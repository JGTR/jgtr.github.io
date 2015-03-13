---
layout: post
title: "Immutability in Ruby applications"
date: 2013-06-16 21:48
comments: true
categories: ruby, programming, immutability, speakerdeck
---

Looking through the programming presentations on [Speakerdeck](www.speakerdeck.com) I found one created recently by Michael Fairley entitled [Immutable Ruby](http://speakerdeck.com/michaelfairley/immutable-ruby).

Michael suggests a technique to ensure that information in an application which is thought of as unchangeable actually stays this way. There are plenty of transformations that can corrupt data, and so I agree that it is probably a good idea to be clear about what should be out of scope for manipulation.

To this end, I will reproduce a few techniques that Michael Fairley puts forward in his presentation. The deck is actually quite long and covers a bunch of other interesting topics (such as: deep freeze with ice nine, and event sourcing), so for more depth please visit his presentation. 


### Creating an immutable module

Lets suppose you are running an online store where you have a purchases table that records previous purchases.The table might look like this:

{% codeblock lang:ruby %}
class Purchase < ActiveRecord::Base
  # t.integer :user_id
  # t.integer :price
  # t.integer :item_id
end
{% endcodeblock %}

There would be little reason to change this data, so it seems likely you would want to make it immutable to protect its integrity. Thus, it would be helpful to have an immutable module, like so:

{% codeblock lang:ruby %}
class Purchase < ActiveRecord::Base
  include Immutable
end
{% endcodeblock %}

To make an immutable module, only 5 lines of code are needed, as follows:

{% codeblock lang:ruby %}
Module Immutable
  def readonly?
    persisted?
  end
end
{% endcodeblock %}

Now, if we try to update data on the purchases table, the change does not go through:

{% codeblock lang:ruby %}
purchase.update_attributes(...) #=>
  ActiveRecord::ReadOnlyRecord
{% endcodeblock %}


### Harnessing active_record with the immutable_attributes gem

Wouldn't it be great to control mutability on certain fields? The immutable_attributes gem allows this:
[github.com/JackDanger/immutable_attributes](http://github.com/JackDanger/immutable_attributes)
*** gem install immutable_attributes ***

Now we can harness ActiveRecord to make the fields on the purchase table immutable, as follows:

{% codeblock lang:ruby %}
class Purchase < ActiveRecord::Base
  attr_immutable :user_id
  attr_immutable :price
  attr_immutable :item_id
end
{% endcodeblock %}


### Using the freeze method

#### The problem

We might start with a method that will build a full url for a part of the application based on some params. This may look as follows:

{% codeblock lang:ruby %}
def build_url(base, path, params={})
  base << "/" unless path.start_with?('/')
  base << path
  base << "?" + params.to_param if params.present?
  base
end

ROOT_URL = "http://example.com"

def example_url(path, params = {})
  build_url(ROOT_URL, path, params)
end
{% endcodeblock %}

Although this code may look like it works, in actuality it may generate some problems as follows:

{% codeblock lang:ruby %}
example_url("blog")
#=> "http://example.url/blog"

example_url("/photos", :sort => "size")
#=> "http://example.url/blog/photos?sort=size"

example_url("/photos", :sort => "size")
#=> "http://example.url/blog/photos?sort=size/photos?sort=size"

{% endcodeblock %}

What is going on here? There is only one instance of the ROOT_URL being created, which is then passed into the build_url method over and over. In the build_url, the **shovel operators (<<) are mutating this single instance of the string**.

#### Fixes

One simple way to fix this would be to replace the "<<" with =+ in the code.

A better way would be to use a freeze method on the ROOT_URL, forcing the application to raise an exception if the string would be mutated. this would look like this:

{% codeblock lang:ruby %}
ROOT_URL = "http://example.com".freeze
{% endcodeblock %}



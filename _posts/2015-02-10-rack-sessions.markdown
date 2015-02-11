---
layout: post
title:  "Ruby and Rack: Working with session"
date:   2015-02-15 09:20:57
categories: ruby rack
---
Rack is a very simple web server interface which you can use to build small web apps. One of the things that I really like about Rack is how minimalistic it is, which makes it a lot less intimidating than other web frameworks (e.g. rails)

This post will include a very small example on working with Rack's session and how to unit test that the session has the correct values. 

To write unit tests for rack, you need to include <code>Rack::Test::Methods</code>. This provides several useful utility methods to test your rack application with.

Lets write a unit test that'll test we can store an object in the session.

{% highlight ruby %}
require 'rack/test'

describe "Rspec Rack Unit Test" do
  include Rack::Test::Methods

  def app
    RackSessionApp.new
  end

  it 'stores Hello World in saved_object' do
    get('/')
    saved_object = last_request.env['rack.session'][:saved_object]
    expect(saved_object).to eq('Hello World')
  end
end
{% endhighlight %}

In order to get access to the session, we can use <code>Rack::Request</code> that provides helper methods.

{% highlight ruby %}
require 'rack'

class RackSessionApp

  def call(env)
    req = Rack::Request.new(env)
    req.session[:saved_object] = 'Hello World'
    [200, {'Content-Type' => 'text/html'}, ['']]
  end
end
{% endhighlight %}

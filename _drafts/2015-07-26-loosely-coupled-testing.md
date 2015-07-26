---
layout: post
title:  Loosely Coupled Testing 
date:   2015/06/29
categories: testing
---
Test Driven Development has a huge amount of benefits, but one that stands out to me is how it helps unify a team. After I have finished test driving some new code, I leave behind a suite of unit tests that the whole team can value from. This suite of tests asserts that the code I have written is (to the best of my abilities) correct. If someone else in the team wants to work on my code, they can use my suite of unit tests as a safety net that'll catch any erroneous changes.

However, there is a risk that this safety net can become too tightly entangled around our code. Things like overusing mocking libraries and unit tests that possess too much knowldge of the system can in fact make the code more cumbersome to work with. Software is in a perpetual state of flux, and we need to be aware of the importance give it the room it needs to evolve.

Before we move on, I would suggest you read The Little Mocker, as I have used the vocabulary that is defined there to differentiate between test doubles/mocks/stubs/fakes/spies and so on.

Mocking Collaborators
=========

One of the fundamental axioms of object oriented programming is for objects to work together with other objects. If it wasn't for this, we will be back to doing procedural programming. This has a profound effect on how we do our testing. When we are test driving a new class X that depends on class Y, then the test for class X also needs to be aware of this dependency.

Example Time. We are working on a `CinemaClient` class that is going to be interacting with a `BoxOffice` in order to check ticket availability/book tickets.

When writing the test for `CinemaClient`, we need to provide it the `BoxOffice` class as well. Unfortunately, the `BoxOffice` has a load of its own dependencies, so we can't easily use the real `BoxOffice` class in the unit test for our `BoxOffice`. Instead, we will be providing a test double that pretends to be the `BoxOffice`.

There's a few ways we can do this, but the more common way that I have observed is to use a mocking library, e.g. JMock/Mockito for Java, rspec-mocks for Ruby. The reason why this approach is so common is because it is relatively quick and easy to do. For example, to stub the box office into returning zero availability, we can do something as follows;

{% highlight ruby %}
it 'searches for availability from box office' do
  expect(test_box_office).to receive(:get_availability).and_return({available_seats: []})
  cinema_client.find_availability
end
{% endhighlight %}

This test knows quite a lot of information with respect to the implementation of our `CinemaClient`. It knows which methods get invoked when the cinema client is finding ticket availability, and is knowledgeable of the response that `box_office.get_availability` is going to be returning.

It turns our that `get_availability` method on the `BoxOffice` is widely used across the system, and the mock setup we have used in the above test is now being copied all over the place. What happens when `get_availability` undergoes some kind of change? What happens if the return value changes from a Hash to some other data type? It is true that a lot of the production code will need changing if this were to happen, but now the tests must also all be painstakingly updated to incorporate the change.

Another disadvantage to using mocking libraries is the amount of test setup that they can require. For e.g, let's say testing a class that interacts with `BoxOffice`, but we aren't explicitly testing those interactions in all of the tests. We want create a stub that returns canned data as follows;

{% highlight ruby %}
before do 
  allow(box_office).to receive(:get_availability).and_return(availability: [])
  allow(box_office).to receive(:reserve_tickets)
  allow(box_office).to receive(:book_tickets)
end 

let(:cinema_client) { CinemaClient.new(box_office) }

it 'finds availability' do
  expect(cinema_client.find_availability).to eq([])
end

it 'reserves tickets' do
  expect(box_office).to receive(:reserve_tickets).with({quantity: 3, movie: "Terminator Genesis"})
  expect(cinema_client.reserve_tickets)
end

it 'books tickets' do
  expect(cinema_client.book_tickets).to eq({status: :booked})
end
{% endhighlight %}

There's quite a lot of test setup here to ensure that all three tests pass. You may imagine this `before` block getting unweildy as more and more changes are added.

So, what is the alternative?

We can hand roll a `FakeBoxOffice` class that can be used across all classes.

{% highlight ruby %}
class FakeBoxOffice
  def get_availability
    {available_seats: []}
  end

  def reserve_tickets(ticket)
    @reserved_tickets << ticket
  end

  def book_tickets
    @booked_tickets << ticket
  end
end
{% endhighlight %}

You may be thinking that this is way more code than was defined when using mocking libraries, so what has this given us?  All mock behaviour in one place to start with. If we we need to change the return value of `get_availability`, we only need to do it here.

Some tests may want to `BoxOffice` to have availability, some may not. We can create constructor methods that control this;

{% highlight ruby %}
class FakeBoxOffice
  def self.has_availability
    @availability = [{movie: "Pixels", quantity: 100}]
    new
  end

  def self.no_availability
    @availability = []
    new
  end
end
{% endhighlight %}

This is how this now looks like in our unit test. Notice that we no longer have a load of mock setup that we don't need.

{% highlight ruby %}
  let(:cinema_client) {CinemaClient.new(FakeBoxOffice.no_availability)}
{% endhighlight %}

Data
==========
Talk about

* test doubles being used, when we can use fixtures instead.

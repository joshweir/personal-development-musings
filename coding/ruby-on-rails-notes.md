# Ruby / Ruby on Rails Notes

## Metaprogramming

create methods using define_method

```ruby
['admin', 'marketer', 'sales'].each do |user_role|
    define_method "#{user_role}?" do
        role == user_role
    end
end
```

## Caching

### Fragment / Russian Doll Caching

Wrap view content in a `cache` block at the top, will hit cache first and only if cache miss will run code within block:

```ruby
cache ['citejson', citation_format, object] do
```

But even better, to use a `cache:` property and render a collection - this takes advantage of multi read cache (single read of cache for multiple records):

```ruby
render partial: 'shortlist/shortlist',
  collection: object,
  as: :object,
  locals: {my_local: my_local},
  cache: Proc.new{|item| [:work_snippet_shortlist, item.work_id]}
```

### Loading static reference data using singleton pattern

I used a singleton pattern to load static data in the current process once, using class variables / class methods:

```ruby
class MyCache
  @@my_static = [1].each{|i| puts 'executing'; 'foo'}
  def self.my_static
    @@my_static
  end
end
puts MyCache.my_static.to_s
```

### Caching DB queries

use [`bulk_cache_fetcher`](https://github.com/justinweiss/bulk_cache_fetcher) gem, which wraps a db query, similar to how fragment cache does.

## ActiveRecord Performance

### `includes`, `preload`, `eager_load`, `joins`

Use the [bullet](https://github.com/flyerhzm/bullet) gem to identify N+1 queries.

```ruby
@persons = Person.all
@persons.each do |person|
  person_mobile = person.mobile
end
#will query persons table once and mobiles for every N persons.
```

`preload` will do a single separate query for each table, `eager_load` will do a single query using `left join` and `includes` will automatically execute `preload` or `eager_load` depending on the nature of the query. `joins` will perform an `inner join`.

```ruby
@persons = Person.eager_load(:mobile).all
@persons.each do |person|
  person_mobile = person.mobile
end
#will perform a single left joined query
#Person.preload(:mobile).all would perform 2 queries, a query for persons and a query for person mobiles
```

Can eager load with these techniques multiple levels deep:

```ruby
@library = Library.all.includes( :books => { :author => :bio } )
```

[Source](https://blog.arkency.com/2013/12/rails4-preloading/)
[Another Source](https://medium.com/@codenode/10-tips-for-eager-loading-to-avoid-n-1-queries-in-rails-2bad54456a3f)

## blocks and `yield`

Generally:
* Use braces { } for single-line blocks
* Use do end for multi-line blocks
But:
* Use do end for procedural blocks - that perform a side effect, but you don't need the return value.
* Use braces { } for functional blocks, that return a value that could be used for chaining.

Blocks (and hence yield) allow a method to invoke it's own code before and/or after a block of code. The block passed to the method is called by the `yield` keyword inside the method. `yield` returns whatever the block returns, it also takes input parameters which means whatever the value in the method that is passed to yield will be available as arguments to the block.

Example:
This shows typical ruby use case to allow an object to be instantiated with a block doing the initialisation.

```ruby
class Car
  attr_accessor :color, :make

  def initialize params={}, &block
    @color = params.fetch(:color, nil)
    @make = params.fetch(:make, nil)
    block.call self if block_given?
    #could also replace block.call with yield
  end
end

# the car is instantiated initializing the color property as a parameter passed to new, and the make property is set in the block passed to new
car = Car.new({color: 'blue'}) {|c|
  c.make = 'toyota'
}
puts "color: #{car.color} make: #{car.make}"
```


Blocks are useful to allow a method to be customized at run time, the block can do any number of side effects before returning the value to be yielded to the method.

def wrap_in_tags(tag, text)
  html = "<#{tag}>#{text}</#{tag}>"
  yield html
end

wrap_in_tags("title", "Hello") { |html| Mailer.send(html) }
wrap_in_tags("title", "Hello") { |html| Page.create(:body => html) }

## TODO

using modules, include and extend

refresh my memory of yielding

http://rubylearning.com/satishtalim/tutorial.html

refresh my memory of the design patterns ive learned, and how they are implemented in ruby:
https://github.com/nslocum/design-patterns-in-ruby

refresh my memory of what i did with scraypa in order to use self class methods using syntax like this (and why):

class << w

when to use curly braces vs do block

clarify what &: means in say reduce or map function exactly

do ruby / rails interview questions

Ways to make Ruby code dryer

refresh Rspec should syntax

Do a (level) check algorithm
Do a binary search algorithm
do a merge sort algorithm
check others

best way to mock activerecord when testing stuff that creates / modifies data

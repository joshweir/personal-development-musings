# Ruby / Ruby on Rails / RSpec Notes

## Ruby Notes

### Metaprogramming

create methods using define_method

```ruby
['admin', 'marketer', 'sales'].each do |user_role|
    define_method "#{user_role}?" do
        role == user_role
    end
end
```

Set instance variables dynamically:

`instance_variable_set("@#{key}", value)`

`class << object` opens a new class structure referencing the metaclass of `object`

```ruby
class Foo
  # self here refers to the class Foo therefore
  # my_static_method is a class method on Foo
  class << self
    def my_static_method
      puts "my_static_method: #{self}"
    end
  end

  def my_instance_method
    # because we are in an instance method,
    # self here refers to the current instance
    # of Foo or instance of any sub class of Foo
    class << self
      puts "my_instance_method: #{self}"
      # inside_method is an instance method
      # associated to the current instance
      # of Foo only (or instance of Foo sub class)
      def inside_method
        puts "inside method: #{self}"
      end
    end
  end
end

foo = Foo.new

class << foo
  def my_singleton_method
    puts "my singleton method: #{self}"
  end
end
foo.my_singleton_method
foo.my_instance_method
Foo.my_static_method
foo.inside_method
#this would error because the inside_method has not yet been defined through a call to my_instance_method
#Foo.new.inside_method
#this would error because the singleton method is only defined against a single instance of Foo (foo):
#Foo.new.my_singleton_method
```

### blocks and `yield`

Generally:
* Use braces { } for single-line blocks
* Use do end for multi-line blocks
But:
* Use do end for procedural blocks - that perform a side effect, but you don't need the return value.
* Use braces { } for functional blocks, that return a value that could be used for chaining.

Blocks (and hence yield) allow a method to invoke it's own code before and/or after a block of code. The block passed to the method is called by the `yield` keyword inside the method. `yield` returns whatever the block returns, it also takes input parameters which means whatever the value in the method that is passed to yield will be available as arguments to the block. I like to think of a block as an optional input argument available to methods.

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

Other common use cases:
* iterators
* managing side effects such as db connections

### Modules / Mixins

```ruby
module AModule
  NUMBERS = [0, 1, 2, 3, 4, 5, 6, 7]
  class AClass
  end
end
#reference module methods / classes
puts "#{AModule::NUMBERS}"
AModule::AClass.new
```

A mixin should be named with an adjective to assist in the single responsibility principle. For example `Taggable`, `Votable`, `Serializable`.

```ruby
# include will make MyModule a direct superclass of
# the MyClass instance making it's methods available
# as would a super classes instance methods
class MyClass
include Taggable
end

# alternatively, an instance could have a module
# dynamically included:
my_class = MyClass.new
my_class.include(Taggable)
```

```ruby
# extend will make MyModule a direct superclass of
# the MyClass's metaclass making it's methods available as class methods to MyClass
class MyClass
extend Taggable
end

# alternatively, an instance could be dynamically
# extended with a module
my_class = MyClass.new
my_class.extend(Taggable)
```

### Inheritance vs Composition (and mixins vs dependency injection) and DRYer code

Techniques for DRYer code:
Move the code to a shared private method if used within the class.
Move the code to another class through:
* Inheritance
* Dependency Injection

Inheritance should only be used sparingly when the child class has an "is-a" relationship with it's parent. Better to avoid the gorilla with a banana problem.

Mixins and dependency injection are usually always the better option than inheritance. Mixins are the more ruby way compared to dependency injection.

Allow code duplication to exist until there are at least 3 instances of code being duplicated, because if you separate out the code into separate mixins or classes too early, you might make the wrong design decision and have to refactor again.

http://weblog.jamisbuck.org/2008/11/9/legos-play-doh-and-programming

## Ruby on Rails Notes

### Caching

#### Fragment / Russian Doll Caching

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

#### Loading static reference data using singleton pattern

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

#### Caching DB queries

use [`bulk_cache_fetcher`](https://github.com/justinweiss/bulk_cache_fetcher) gem, which wraps a db query, similar to how fragment cache does.

### ActiveRecord Performance

#### `includes`, `preload`, `eager_load`, `joins`

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

### Controller / View / Model stuff

To create / update models and nested associated models:

```ruby
class Member < ActiveRecord::Base
  has_many :posts
  accepts_nested_attributes_for :posts, reject_if: :reject_posts

  def reject_posts(attributes)
    attributes['title'].blank?
  end
end

# in the controller action, with params =
# { member: { name: 'john'
#  posts_attributes: [
#  { id: '2', _destroy: '1' },
#  { id: '3', title: 'a title', content: 'some content'}]
# }}
# will update the member name = john, will create / update post with id = 3 and will delete post with id = 2
if @member.update(member_params)
    @member.save
end

#...user_params is in private methods:
private

def member_params
  params.require(:member).permit(:name,
    :post_attributes => [:id, :title, :content])
end
```

## RSpec notes

### expect vs should syntax

```ruby
foo.should eq(bar)
foo.should_not eq(bar)
```

same as

```ruby
expect(foo).to eq(bar)
expect(foo).not_to eq(bar)
```

### controller stuff

`assigns(:contacts)` retrieves the instance variable `@contacts` within the subject controller.

### Capybara cheat sheet

https://gist.github.com/zhengjia/428105

## TODO

http://rubylearning.com/satishtalim/tutorial.html

refresh my memory of the design patterns ive learned, and how they are implemented in ruby:
https://github.com/nslocum/design-patterns-in-ruby


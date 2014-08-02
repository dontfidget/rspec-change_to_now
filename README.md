# RSpec::ChangeTo [![Build Status](https://travis-ci.org/dontfidget/rspec-change_to_now.png)](https://travis-ci.org/dontfidget/rspec-change_to_now)

RSpec::ChangeTo provides the `to`, `not_to` and `to_not` methods to `change` matcher to describe changes in the conditions

## Installation

Add this line to your application's Gemfile:

    gem 'rspec-change_to'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install rspec-change_to_now

And require it as:

    require 'rspec/change_to_now'

## Usage

Use the `to_now` and `not_to_now` methods to make assertions about the effect of an rspec `change` block.

```ruby
    expect { @x = 1 }.to change { @x }.to_now eq 1
```

and

```ruby
    expect { @x = 1 }.to change { @x }.not_to eq 2
```

The method `to_now` will check both that the matcher *does not* match prior to the change and that it *does* match after the change.  The method `not_to_now` (`not_to` for short) will do the opposite, ensuring that the matcher matches prior to the change, and fails only after the change.  All methods will ensure that a change actually takes place. 

Also supported are aliases for those who don't want to split their infinitives and for those who would like to differently split them:

* `to_now` can also be called as `now_to`
* `not_to_now` can also be called `not_to`, `to_not`, `to_not_now` and `not_now_to` 

## Why is this useful?

`change { }.from().to()` adds expectation of pre- and post-conditions for a change, but it is restricted only to object values.  With `to_now`, you can write

```ruby
    list = []
    expect { list << :a }.to change { list }.to_now include :a
```

 whereas previously you would have to fully specify the original and final values of the list: 

```ruby
    list = []
    expect { list << :a }.to change { list }.from([]).to([:a])
```

While that may not seem like a big deal, the real values comes in more complex statements like:

```ruby
    person = Person.create(name: 'Taylor')
    expect { person.siblings.create(name: 'Sam') }.to change { Person.all.map(&:name) }.to_now include('Taylor')
```

Arguably, I should be injecting some dependencies here instead of relying on globals, but Rails code doesn't always look like that.  I'm looking forward to playing around with this and seeing if it really helps simplify specs.  I'd love to hear your feedback.

## `include(&block)` matcher

This gem also augments `include` to take a block, so it behaves like `satisfy` over an enumerable.  You can use it like so:


```ruby
    list = []
    expect { list << 1 }.to change { list }.to include?(&:even?)
```

Or more interestingly:

```ruby
    person = Person.create(name: 'Taylor')
    expect { person.siblings.create(name: 'Sam') }.to change {
      Person.all
    }.to_now include { |person|
      person.name == 'Taylor'
    }
```

It is also aliased as `include_any`.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
Testing
====================

***Man, I just love unit tests, I've just been able to make a bunch of changes to the way something works, and then was able to confirm I hadn't broken anything by running the test over it again.***

> **Types:**

>- Unit tests;
>- Acceptance tests;



Unit tests
----------------

The lowest and most important level. Confirm the correct behavior of isolated units of functionality:

- Classes
- methods
- functions

***and run extremely fast (milliseconds).***


Tools: 

- RSpec
- MiniTest
- Jasmine(JavaScript)

Acceptance tests
----------------

Acceptance tests — The high level (typically user-level) tests.

Since they run a lot more code than unit tests and often depend on external services they are much slower (seconds or minutes).

Tools:

- RSpec and Capybara
- Cucumber
- Selenium

----------------
----------------

RSpec
====================

> **Topics:**

>- Install Rspec +;
>- Configuration +;
>- Basic Structure +;
>- Matchers;
>- DRY Specs +;
>- Hooks & Tags;
>- Mocks & Stubs;
>- Custom Matchers;

Install Rspec
----------------

In order to run rspec, you need to install rspec via *** Gemfile***

```ruby
# Gemfile
source "https://rubygems.org"

gem "rspec"
```

and run

```bash
$ bundle install
```

create spec directory

```bash
$ mkdir spec
```

and to run spec execute

```bash
$ bundle exec rspec
```

Configuration
----------------

***Stores runtime configuration information.***

Configuration options are loaded from **~/.rspec**, **.rspec**, **.rspec-local**, command line switches, and the **SPEC_OPTS** environment variable (listed in lowest to highest precedence; for example, an option in **~/.rspec** can be overridden by an option in **.rspec-local**).

#### Standard settings

```ruby
RSpec.configure do |c|
  c.drb          = true
  c.drb_port     = 1234
  c.default_path = 'behavior'
end
```

#### Hooks

```ruby
RSpec.configure do |c|
  c.before(:suite)   { establish_connection }
  c.before(:example) { log_in_as :authorized }
  c.around(:example) { |ex| Database.transaction(&ex) }
end
```

[More available confis](http://www.rubydoc.info/github/rspec/rspec-core/RSpec/Core/Configuration)

#### spec_helper file

For our specs to run, we need to require the Ruby classes we’re testing inside the spec_helper file. Also, in each spec file we are describing, we need to require this spec_helper file.

spec_helper file also is ofter used for configuration, and adding new dependencies.

```ruby
# spec/spec_helper.rb
require_relative '../library'
require_relative '../book'
 
require 'yaml'
```

```ruby
# spec/classes/book.rb
require 'spec_helper'
 
describe Book do
 
end
```

Basic Structure
----------------

####describe

**RSpec** gives you a way to encapsulate what you’re testing via the ***describe*** block, and it’s friend ***context***. In a general unit testing sense, we use ***describe*** to describe the behavior of a class:

```ruby
# spec/hash_spec.rb
describe Hash do
 
end
```

#### example output

Blank tests:

```bash
$ spec spec/hash_spec.rb

Finished in 6.0e-06 seconds

0 examples, 0 failures
```

With some examples:

```bash
$ spec spec/hash_spec.rb --format specdoc

Hash
- should be callable by [key]

Finished in 0.022865 seconds

1 example, 0 failures
```

#### context

The ***context*** method does the same thing by letting you contextualize a block of your tests.

```ruby
describe Burger do
  describe "#apply_ketchup" do
    context "with ketchup" do
      before do
        @burger = Burger.new(:ketchup => true)
        @burger.apply_ketchup
      end
 
      it "sets the ketchup flag to true" do
        @burger.has_ketchup_on_it?.should be_true
      end
    end
 
    context "without ketchup" do
      before do
        @burger = Burger.new(:ketchup => false)
        @burger.apply_ketchup
      end
 
      it "sets the ketchup flag to false" do
        @burger.has_ketchup_on_it?.should be_false
      end
    end
  end
end
```


#### it
Tests are written using the ***it*** block. Here’s an example of how you might write a spec for the Hash class:

```ruby
# spec/hash_spec.rb
describe Hash do
  it "should return a blank instance" do
    Hash.new.should == {}
  end
end
```

#### before and after

You can set up test state using the ***before*** and ***after*** directives. This will apply to anything in your ***describe*** block:

```ruby
describe Hash do
  before do
    @hash = Hash.new({:hello => 'world'})
  end
 
  it "should return a blank instance" do
    Hash.new.should == {}
  end
 
  it "hash the correct information in a key" do
    @hash[:hello].should == 'world'
  end
end
```

Matchers
----------------

#### Equivalence

```ruby
expect(actual).to eq(expected)  # passes if actual == expected
expect(actual).to eql(expected) # passes if actual.eql?(expected)
expect(actual).not_to eql(not_expected) # passes if not(actual.eql?(expected))
```

Note: The new `expect` syntax no longer supports the `==` matcher.

#### Identity

```ruby
expect(actual).to be(expected)    # passes if actual.equal?(expected)
expect(actual).to equal(expected) # passes if actual.equal?(expected)
```

#### Comparisons

```ruby
expect(actual).to be >  expected
expect(actual).to be >= expected
expect(actual).to be <= expected
expect(actual).to be <  expected
expect(actual).to be_within(delta).of(expected)
```

#### Regular expressions

```ruby
expect(actual).to match(/expression/)
```

Note: The new `expect` syntax no longer supports the `=~` matcher.

#### Types/classes

```ruby
expect(actual).to be_an_instance_of(expected) # passes if actual.class == expected
expect(actual).to be_a(expected)              # passes if actual.kind_of?(expected)
expect(actual).to be_an(expected)             # an alias for be_a
expect(actual).to be_a_kind_of(expected)      # another alias
```

#### Truthiness

```ruby
expect(actual).to be_truthy   # passes if actual is truthy (not nil or false)
expect(actual).to be true     # passes if actual == true
expect(actual).to be_falsy    # passes if actual is falsy (nil or false)
expect(actual).to be false    # passes if actual == false
expect(actual).to be_nil      # passes if actual is nil
expect(actual).to_not be_nil  # passes if actual is not nil
```

#### Expecting errors

```ruby
expect { ... }.to raise_error
expect { ... }.to raise_error(ErrorClass)
expect { ... }.to raise_error("message")
expect { ... }.to raise_error(ErrorClass, "message")
```

#### Expecting throws

```ruby
expect { ... }.to throw_symbol
expect { ... }.to throw_symbol(:symbol)
expect { ... }.to throw_symbol(:symbol, 'value')
```

#### Yielding

```ruby
expect { |b| 5.tap(&b) }.to yield_control # passes regardless of yielded args

expect { |b| yield_if_true(true, &b) }.to yield_with_no_args # passes only if no args are yielded

expect { |b| 5.tap(&b) }.to yield_with_args(5)
expect { |b| 5.tap(&b) }.to yield_with_args(Fixnum)
expect { |b| "a string".tap(&b) }.to yield_with_args(/str/)

expect { |b| [1, 2, 3].each(&b) }.to yield_successive_args(1, 2, 3)
expect { |b| { :a => 1, :b => 2 }.each(&b) }.to yield_successive_args([:a, 1], [:b, 2])
```

#### Predicate matchers

```ruby
expect(actual).to be_xxx         # passes if actual.xxx?
expect(actual).to have_xxx(:arg) # passes if actual.has_xxx?(:arg)
```

#### Ranges (Ruby >= 1.9 only)

```ruby
expect(1..10).to cover(3)
```

#### Collection membership

```ruby
expect(actual).to include(expected)
expect(actual).to start_with(expected)
expect(actual).to end_with(expected)

expect(actual).to contain_exactly(individual, items)
# ...which is the same as:
expect(actual).to match_array(expected_array)
```

#### Examples

```ruby
expect([1, 2, 3]).to include(1)
expect([1, 2, 3]).to include(1, 2)
expect([1, 2, 3]).to start_with(1)
expect([1, 2, 3]).to start_with(1, 2)
expect([1, 2, 3]).to end_with(3)
expect([1, 2, 3]).to end_with(2, 3)
expect({:a => 'b'}).to include(:a => 'b')
expect("this string").to include("is str")
expect("this string").to start_with("this")
expect("this string").to end_with("ring")
expect([1, 2, 3]).to contain_exactly(2, 3, 1)
expect([1, 2, 3]).to match_array([3, 2, 1])
```

#### `should` syntax

In addition to the `expect` syntax, rspec-expectations continues to support the
`should` syntax:

```ruby
actual.should eq expected
actual.should be > 3
[1, 2, 3].should_not include 4
```

##### Other `should` Matchers

```ruby
obj.should be_true
obj.should be_false
obj.should be_nil
obj.should be_empty
obj.should exist
obj.should have_at_most(n).items
object.should have_at_least(n).items
obj.should include(a[,b,...])
obj.should match(string_or_regex)
obj.should raise_exception(error)
obj.should respond_to(method_name)
```

DRY Specs
----------------

#### let

The example from the ***context*** works but can become a bit tiresome to repeat all the time. RSpec gives us ***let*** helper method to generalize it.

```ruby
describe Burger do
  describe "#apply_ketchup" do
    context "with ketchup" do
      let(:burger) { Burger.new(:ketchup => true) }
      before  { burger.apply_ketchup }
 
      it "sets the ketchup flag to true" do
        burger.has_ketchup_on_it?.should be_true
      end
    end
 
    context "without ketchup" do
      let(:burger) { Burger.new(:ketchup => false) }
      before  { burger.apply_ketchup }
 
      it "sets the ketchup flag to false" do
        burger.has_ketchup_on_it?.should be_false
      end
    end
  end
end
```

#### subject

We can clean it up even further using the ***subject*** method

```ruby
describe Burger do
  describe "#apply_ketchup" do
    subject { burger }
    before  { burger.apply_ketchup }
 
    context "with ketchup" do
      let(:burger) { Burger.new(:ketchup => true) }

 	   it "sets the ketchup flag to true" do
        subject.has_ketchup_on_it?.should be_true
      end
    end

  end
end
```

#### specify

The ***specify*** method is just like the ***it*** method except the ***specify*** method takes the code block as the description of the test:

```ruby
describe Burger do
  describe "#apply_ketchup" do
    subject { burger }
    before  { burger.apply_ketchup }
 
    context "with ketchup" do
      let(:burger) { Burger.new(:ketchup => true) }
 
      specify { subject.has_ketchup_on_it?.should be_true }
    end
 
    context "without ketchup" do
      let(:burger) { Burger.new(:ketchup => true) }
 
      specify { subject.has_ketchup_on_it?.should be_false }
    end
  end
end
```

Hooks & Tags
------------

####Filtering examples

The simplest use case is to ***tag*** certain examples with ***keywords***, and then tell RSpec to decide whether to run examples based on that keyword.

For example, you might focus your next test run on only those examples you are currently developing by using a “focus” keyword:

```ruby
RSpec.configure do |c|
  c.filter_run_including current: true
  c.run_all_when_everything_filtered = true
end
```

```ruby
describe Post
  describe '#to_param', focus: true do
    it 'returns the slug attribute'
  end
end
```

RSpec will now only run examples tagged with ***focus***. When no examples are tagged, it will run everything. Alternatively, you could filter examples from the command line:

```bash
$ rspec --tag focus:true
```

Alternative RSpec version >= 3.0

```ruby
RSpec.configure do |c|
  c.treat_symbols_as_metadata_keys_with_true_values = true
end
```

```ruby
describe Post
  describe '#to_param', :focus do
    it 'returns the slug attribute'
  end
end
```

####Altering how examples are run

[ResqueSpec](https://github.com/leshill/resque_spec) is a nice library to fake running background jobs with [Resque](https://github.com/resque/resque). It will queue jobs in a simple in-memory hash, which allows you to easily set expectations on what gets queued:

```ruby
it 'mails a PDF form to the user' do
  User.create! email: 'foo@example.com'
  expect(PdfMailerJob).to have_queue_size_of(1)
end
```


####Altering example context

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
>- Basic Structure +;
>- Configuration +;
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


Basic Structure
----------------

####describe

**RSpec** gives you a way to encapsulate what you’re testing via the ***describe*** block, and it’s friend ***context***. In a general unit testing sense, we use ***describe*** to describe the behavior of a class:

```ruby
# spec/hash_spec.rb
describe Hash do
 
end
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

####Altering how examples are run

####Altering example context

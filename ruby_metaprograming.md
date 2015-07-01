Ruby Metaprogramming
====================


> **Programming of `'Programming'`.**

>- A way to add, edit or modify the code of your program while it’s running;
>- Reopen or modify existing classes;
>- Catch methods that don’t exist;
>- Avoid repetitious coding to keep your program DRY.



The Object Model
----------------

'**Practically everything in Ruby is an Object!**'
with the exception of control structures

> **Concepts:**

> - Class == object
> - Open Classes
> - Callable objects

---

### Class is an object
**Object** is the default root of all Ruby ***objects***. Object inherits from **BasicObject** which allows creating alternate object hierarchies.

```ruby
1.class #=> Fixnum
1.is_a? Object #=> true
Fixnum.is_a? Object #=> true
```

Basically the key thing to understand is that every ***class*** is an ***instance*** of the **Class** ***class*** and every ***class*** is a ***subclass*** of **Object**.

```ruby
Object.instance_of? Class #=> true
Fixnum.instance_of? Class #=> true
```

---

### Open Classes
```ruby
class Object
	def better_debug
		p "------------#{to_s}" # to_s == self.to_s
	end
end

1.better_debug #=> "------------1"
'email@example.com'.better_debug #=> "------------email@example.com"
```

### Callable objects

When you call a method, you usually do so using the standard dot notation:

```ruby
class MyClass  def my_method(my_arg)    my_arg * 2  end
endobj = MyClass.newobj.my_method(3)  # => 6
```

but there is an alternative:

```ruby
obj.send(:my_method, 3)   # => 6
```

Or more complex example:

```ruby
class Profile
  attr_accessor :name, :age, :email, :password 
end

users = [{
	name: 'Steve',
	age: 23,
	email: 'steve.smith@example.com',
	password: 'supersecurepassword' 
},{
	name: 'John',
	age: 30,
	email: 'john.simpson@example.com',
	password: 'supersecurepassword' 
},{
	name: 'Mary',
	age: 57,
	email: 'mary.piter@example.com',
	password: 'supersecurepassword' 
}]

profiles = users.map do |user_setting|
	profile = Profile.new
	user_setting.each_key do |key|
		profile.send(key+'=', user_settings[key])
	end
	profile
end

``` 

-------
-------

Methods
----------

Like in SmallTalk Ruby has this idea of sending messages between objects.

> **Concepts:**

> - Dynamic methods
> - method_missing
> - ***self*** receiver, and ***Methods lookup***

###  Dynamic methods

***How to define methods dynamically and remove the duplicated code.***

```ruby
define_method :my_method do |my_arg|	my_arg * 3endmy_method(2)  # => 6
```

Inside different receiver

```ruby
class Rubyist
  define_method :hello do |my_arg|
    my_arg
  end
end
obj = Rubyist.new
puts(obj.hello('Matz')) # => Matz
```

---

#### method missing

```ruby
class Rubyist
  def method_missing(m, *args, &block)
    str = "Called #{m} with #{args.inspect}"
    if block_given?
      puts str + " and also a block: #{block}"
    else
      puts str
    end
  end
end

# => Called anything with []
Rubyist.new.anything
# => Called anything with [3, 4] and also a block: #<Proc:0xa63878@tmp.rb:12>
Rubyist.new.anything(3, 4) { something }
```

---

### ***self*** and the default receiver
When you call a method, Ruby does two things:

- It finds the ***method***. This is a process called **method lookup**.
- It executes the ***method***. To do that, Ruby needs something called **self**.

```ruby
obj.method()
```

When you call a ***method***, Ruby looks into the object's ***class*** and finds the ***method*** there. We need to know about two new concepts: the ***receiver*** and the ***ancestors chain***. The ***receiver*** is simply the ***object*** that you call a ***method*** on.

For example, if you write ***obj.method()***, then ***obj*** is the ***receiver***.

To understand the concept of an ancestors chain, just look at any **Ruby** ***class***. Then imagine moving from the ***class*** into its ***superclass***, then into the ***superclass's*** ***superclass***, and so on until you reach **Object** (the default superclass) and then, finally, **BasicObject** (the root of the Ruby class hierarchy). The path of classes you just traversed is called the ***"ancestors chain"*** of the class (the ancestors chain also includes modules).

```ruby
1.class.ancestors
=> [Fixnum,
 JSON::Ext::Generator::GeneratorMethods::Fixnum,
 Integer,
 Numeric,
 Comparable,
 Object,
 PP::ObjectMixin,
 ActiveSupport::Dependencies::Loadable,
 JSON::Ext::Generator::GeneratorMethods::Object,
 Kernel,
 BasicObject]
```

---

#### self

In **Ruby**, ***self*** is a special variable that always references the ***current object***.

```ruby
class Cat
  attr_reader :name
  def initialize(name)
    @name = name
  end
  
  def talk
    # This can be written as "#{name} may" because any Cat object
    # here will be a default receiver
    p "#{self.name} may"
  end
end

cat = Cat.new('Tom')
cat.talk() #=> Tom may  
    
```

----
----

Blocks
----------

One of the most confusing parts of learning basic Ruby (until your AHA! moment) is understanding what blocks are and how they work

Commonly used as inputs to some of the iterators you've no doubt worked with like **each** or **map**.

> **Concepts:**

> - Defining and calling blocks
> - Closures
> - DSL's 

---

#### Defining and calling blocks

You declare a block using squiggly braces {} if it's on one line or do ... end if it's on multiple lines

```ruby
[1,2,3].each { |num| print "#{num}! " } #=> 1! 2! 3! =>[1,2,3]

# Identical to the first case.
[1,2,3].each do |num|
   print "#{num}!"
end
#=> 1! 2! 3! =>[1,2,3]
```

Blocks are used as arguments to other methods (like #each)

```ruby
[1,2].each { |num| p num }  # here num is an argument
```

Just like ***methods***, some ***blocks*** take inputs, others do not. Some return important information, others do not. **Blocks** let you use the ***implicit return*** (whatever's on the last line) but **NOT return**, since that will ***return you from whatever method actually called the block***.

---

##### yield

```ruby
class Array 
  def my_each
    i = 0
    while i < self.size
        yield(self[i])  
        i+=1      
    end
    self
  end
end

[1,2,3].my_each { |num| print "#{num}!" } #=> 1! 2! 3!
```

If you want to ask whether a ***block*** was passed at all (to only yield in that case), use #block_given?, or rather:

```ruby
yield if block_given?
```

---

##### Procs, aka Procedures!

What if you want to pass TWO blocks to your function? What if you want to save your block to a variable so you can use it again later? 

```ruby
my_proc = Proc.new { |arg1| print "#{arg1}! " }
[1,2,3].each(&my_proc) #=> 1! 2! 3!
```

An alternative to call may be ***call*** method.

```ruby
my_proc.call("howdy ") #=> howdy !
```

---

### Closures

Blocks and Procs are both a type of "closure". A closure is basically a formal, computer-science-y way of saying "a chunk of code that you can pass around but which hangs onto the variables that you gave it when you first called it"

Closure is just the umbrella term for all four of those things:

- Blocks;
- Procs;
- Lambdas;
- Methods.

which all somehow involve passing around chunks of code.

```ruby
def my_method	x = "Goodbye"	yield("cruel" )
endx = "Hello"my_method {|y| "#{x}, #{y} world" } # => "Hello, cruel world"
```

---
---


Class
----------


> **Note:**

> - Singleton Methods
> - Eigen Class

---

### Singleton Methods

Since all methods are implemented and stored by the class definition, it should be impossible for an object to define its own methods. However, Ruby provides a way around this - you can define methods that are available only for a specific object. Such methods are called Singleton Methods.

```ruby
class Cat 
  # no methods here
end

cat = Cat.new
def cat.may
  p "May!"
end
cat.may #=> May!
```

- ***Objects*** in Ruby only store the ***state***. Its behaviour comes from its ***class definition***.
- Objects can also have methods that are ***independent*** of the parent class definition. They are called ***singleton methods*** and are stored on the ***metaclass*** of the object. The metaclass is typically invisible to the programmer. Metaclass is also called: **EigenClass** or **SinglenotClass**.

```ruby
class Object
  def metaclass
    class << self
      self
    end
  end
end


a=Object.new
p a.metaclass.new
```

---

### Eigenclass

Eigenclass is where your singleton methods are stored.

```ruby
class Dog
end

snoopy = Dog.new
=> #<Dog:0x00000001c4a170>

snoopy.eigenclass
=> #<Class:#<Dog:0x00000001c4a170>>

snoopy.eigenclass.superclass
=> Dog
```

All ***methods** defined through ***eigenclass** will be available only for ***objects*** on  which they were defined.

```ruby
class Cat
  #... empty class definition;
end

cat1 = Cat.new
cat2 = Cat.new

class << cat1
  def may
    p 'may'
  end
end

cat1.may() #=> may
cat2.may() #=> NoMethodError: undefined method `may' for cat2:Cat
```

---
---

Coding the code.
----------


> **Concepts:**
 
> - Karnel#eval
> - Karnel#class_eval
> - Karnel#instance_eval

---

#### Karnel#eval

***eval()*** executes any string of the code, passed as a string.
Executing a ***string of Ruby code*** is a pretty pointless, but the power of **eval()** becomes apparent when you ***compute(create)*** your Strings of Code ***on the fly***

```rubyarray = [10, 20]element = 30eval("array << element") # => [10, 20, 30]
```
The Capistrano Example (ruby metaprograming book)

Capistrano is a framework for automating the deployment of Ruby applications.

One of default tasks capistrano provides is: deploy:update. In older versions deploy:update was called simply update.

```ruby
namespace :deploy do
  task :update do    # ...  end
end
``` 

But what if you want to execute an old build file (one that uses the non-namespaced update task) on a recent version of Capistrano? To cater to this scenario, Capis- trano provides a backward-compatibility feature:

```ruby
map = { 
	"update" => "deploy:update",
	"restart" => "deploy:restart",
	"cleanup" => "deploy:cleanup",
	# ...}
map.each do |old, new|
  # ...  eval "task(#{old.inspect}) do    warn "[DEPRECATED] `#{old}' is deprecated. Use `#{new}' instead."    find_and_execute_task(#{new.inspect})  end"
end
```

---

#### Karnel#class_eval

***class_eval*** evaluates the string or block in the ***context*** of the **Module** or **Class**. So the following pieces of code are equivalent:

```ruby
class String
  def lowercase
    self.downcase
  end
end

String.class_eval do
  def lowercase
    self.downcase
  end
end
```

In each case, the String class has been reopened and a new method defined. That method is available across all instances of the class, so:

```ruby
"This Is Confusing".lowercase 
=> "this is confusing"
"The Smiths on Charlie's Bus".lowercase
=> "the smiths on charlie's bus"
```

Adventages over simple class reopening are: 

- You can execute a string of the code;
- If class in not defined, you'll gen an exception;

```ruby
Aray.class_eval do
  #...
end

#=> NameError: uninitialized constant Aray
```

---

#### Karnel#instance_eval

***instance_eval*** on the other hand evaluates code against a single ***object instance***:

```ruby
string = "This Is String Object"
string.instance_eval do
  def make_lowercase
    self.downcase
  end
end   

string. make_lowercase #=> "this is string object"

"Second String object".make_lowercase
#=> NoMethodError: undefined method ‘make_lowercase’ for "Second String object":String
```

So with ***instance_eval***, the method is only defined for that single instance of a string. The same if using singlenot methods.

Remember that any class is an instance of a Class class, and therefor is an object ?
What I want to say is:  ***instance_eval*** on a Class will define singleton class methods. Or simply class methods.

Just as "This Is String Object" and "Second String object" are both String instances, Array, String, Hash and all other classes are themselves instances of Class.

So when we call instance_eval it does the same on a class as it would on any other object. If we use instance_eval to define a method on a class, it will define a method for just that instance of class, not all classes. We might call that method a class method, but it is just an instance method for that particular class.
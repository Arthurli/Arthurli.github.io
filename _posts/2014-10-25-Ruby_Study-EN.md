---
layout: post
title: Ruby Study Memo
date: 2014-10-25
categories: ruby
---

Leran this at www.codecademy.com. Great online course.I picked up some key word from this lession for memo.

<!-- more -->

#Basic

1. There's always more than one way to do something in Ruby.
2. Everything in Ruby is an Object.

##Input

~~~
variable = gets.chomp
~~~
`chomp` removes that extra line.

##Operation

* create with empty hash

~~~ruby
file = {}
~~~

* need space between variable and operation

~~~ ruby
Error: a++
Fine: a += 1 
~~~

##Control Flow

### unless == if(!var)


##Loops & Iterators


###until
~~~
i = 0
until i == 6
  i += 1
end
puts i
# ==> 6
~~~


###Loop
~~~
i = 20
loop do
  i -= 1
  print "#{i}"
  break if i <= 0
end
#==>191817161514131211109876543210
~~~
* The `next` keywords can be used to skip over certain steps in the loop.

~~~
i = 20
loop do
  i -= 1
  next if i % 2 == 1
  print "#{i}"
  break if i <= 0
end
#==>181614121086420
~~~

###Inclusive and Exclusive Ranges

* `1..15` not include 15
* `1...15` inlcude 15 

##Iterator

can apply an expression to each element of an object.
~~~
object.each { |item| # Do something }
~~~

The `.times` methods is like a super compact `for` loop: it can perform a task on each item in a object a specified number of times.
~~~
10.times { print "Chunky bacon!" }
~~~


##Block,Proc,Lambda

`Block`: A Ruby block is just a bit of code that can be excuted.

###Bolck

~~~ruby

my_nums = [1,2,3]
#.collection
my_nums.collection { |num| num ** 2 }
# return [1,4,9]
my_nums.collection! { |num| num ** 2 }
# return [1,4,9] && my_nums == [1,4,9]


#.each
[1, 2, 3].each do |num|
  puts num
end
# ==> Prints 1, 2, 3 on separate lines
[1, 2, 3].each { |num| puts num }
~~~

###Yied

~~~ruby
def block_test
  puts "We're in the method!"
  puts "Yielding to the block..."
  yield
  puts "We're back in the method!"
end

block_test { puts ">>> We're in the block!" }


def double(n)
    puts "Ori input is #{n}"
    yield(n)
end

double(2) { |n| puts n *2}
~~~

###Proc
Procs are easy to define! You just call `Proc.new` and pass in the block you want to save.

* Procs are full-fledged object.
* This prevents you from haveing to retype the contents of your block every time you need to execute a particular bit of code.


~~~ruby
multiples_of_3 = Proc.new do |n|
  n % 3 == 0
end

(1..100).to_a.select(&multiples_of_3)

multiples_of_3.call
~~~

###Lambda

~~~
lambda { |param| block }

lambda { puts "Hello!" }
~~~


###Lambda VS. Proc

* A lambda checks the number of arguments passed to it , while a proc does not. This means that a lambda will throw an error if you pass it the worng number of arguments, whereas a proc will ignore unexpected arguments and assign `nil` to any that are missing.
* When a lambda returns , it passes control back to the calling methods; when a proc returns, it does so immediately, without going back to the calling methods.




# Class 

### Create a class

~~~ruby
class Message
    @@messages_sent = 0
    def initialize(from, to)
        @from = from
        @to = to
        @@messages_sent += 1
    end
end

my_message = Message.new("wing.of.war","Zhangejue")
~~~



if you want to end a Ruby statement without going to a new line, you can just type a semicolon.

~~~ruby
class Monkey
end

to 

class Monkey;end
~~~

### Set & Get

That `name=` might look funny, but you're allowed to put an `=` sign in a methods name.
~~~
def name
  @name
end

def name=(value)
  @name = value
end

~~~
*equal*
~~~
attr_reader :name
attr_writer :job
attr_accessor :job
~~~

##Class Variable

* We can create class variable by starting a variable name with two @
* Only one copy of a class variable shared by all instances of a class.

~~~ruby
class MyClass
	@@class_variable
end
~~~

* class variable should use a class method to grab it.


##Inheritance

~~~ruby
class ChildClass < FatherClass
end
~~~

* ChildClass is called: **derived class** Or **subclasss**
* FatherClass is called: **parent** or **superclass**
* **derived class** can only have one **superclass**, not support **multiple inheritance**


~~~ruby
class DerivedClass < Base
  def some_method
    super(optional args)
      # Some stuff
    end
  end
end

class Email<Message
    def initialize(from, to)
        super
    end
end

~~~

##Public&Private

* Note that everything after the **public** keyword through the **end** of the class definition will now be public unless we say otherwise.
* Another way to say this is that the method cannot be called with an explicit receiver.
* In order to access private information, we have to create public methods that know how to get it.


#Module

You can think of a **Module** as a toolbox that contains a set of methods and constants.

One of the main purposes of modules is to seprate methods and constants into named spaces. That is called `namespacing`.


* It dosent't make sense to include variables in modules, since variables change.

~~~ruby
module MyLibrary
    FAVE_BOOK = "Harry porter"
end
~~~

* `Math::PI` and `Circle::PI`, This is called the **scope resolution operator**


####inlcude a module...
~~~ruby
class Angle
  include Math
  attr_accessor :radians
  
  def initialize(radians)
    @radians = radians
  end
  
  def cosine
    cos(@radians)
  end
end
~~~

##Mixin

When a module is used to mix addtional behavior and information into a class, it's called **mixin**

* `include` mixed a module's methods in at the instance level.
* `extend` keyword mixes a module's methods at the `class` level.


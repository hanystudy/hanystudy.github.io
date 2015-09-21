---
layout: post
title: "metaprogramming ruby: core concepts - Object Oriented Hierarchies"
date: 2015-08-29 22:40:06 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

Since last several articles, we've gone through all basic but the most important internal definitions in Ruby lang. They are BasicObject, Object, Module, and Kernel, don't forget Class. The five composite the root of Object Oriented Hierarchies in Ruby world. One of their most useful benefit is incredible metaprogramming ability, which already provides people revolutionary tools like Ruby on Rails. Frankly speaking, the word metaprogramming makes itself too mysterious to be accepted by most people. Conciseness and Expressiveness - are the only purpose of this feature.

###1. Class
Classes in Ruby are first-class objects, each class in Ruby is an instance of class Class. Typically, you create a new class by using: Class.new. When a new class is created, an object of type Class is initialized and assigned to a global constant (Name in this case). When Name.new is called to create a new object, the new method in Class is run by default. Classes, modules, and objects are interrelated, we will discuss more in next chapter.

####Public Class Methods

#####new
Creates a new anonymous (unnamed) class with the given superclass (or Object if no parameter is given). You can give a class a name by assigning the class object to a constant. If a block is given, it is passed the class object, and the block is evaluated in the context of this class using class_eval.

####Public Instance Methods

#####allocate
Allocates space for a new object of class's class and does not call initialize on the new instance. The returned object must be an instance of class.

#####new
Calls allocate to create a new object of class's class, then invokes that object's initialize method, passing it args. This is the method that ends up getting called whenever an object is constructed using .new.

#####superclass
Returns the superclass of class, or nil. Returns nil when the given class does not have a parent class.

####Private Instance Methods

#####inherited
Callback invoked whenever a subclass of the current class is created.

###2. Hierarchy of Five Kings

For more clearly, here we give a figure to cover all five items listed in preface. (We also list two guests in this figure to represent custom codes in the hierarchy)

![More advance Ruby OO hierarchy](http://7xk84n.com1.z0.glb.clouddn.com/metaprogramming_ruby/ruby_oo_hierarchy.png)

From the figure, we can see that class can be completely separated with objects under well design. Ruby uses this kind of hierarchy to implement its internal Object Oriented design. Here we would like to introduce the responsibilities on metaprogramming for each in above figure, and have deeper discussion on Module and Class, which are directly related to custom codes.

####BasicObject

BasicObject is the root of all objects in Ruby through its subclass Object. The only class method in BasicObject is 'new', which is used to allocate memory to new object, and do initialization. For the public instance methods, mainly including object_id, send, and instance_eval/instance_exec to provide basic operations on any objects in Ruby. Also, BasicObject figures out the method_missing mechanism, which is very important for Ruby DSL definition, and singleton methods hook — which actually connects classes and objects in Ruby.

####Object

If any class is defined by using keyword 'class' without other specific inheritance, it should be inherited from Object by default. Object offers the top useful Constants in Ruby world, like ARGV, ENV, RUBY_VERSION, STDIN/STDOUT/STDERR, etc. Also for public instance methods, it provides objects comparison, clone/dup, freezing, and respond_to? checking, etc.

In metaprogramming field, Object mainly focus on instance level usage, like getting class of object, extend, instance_of?, methods, retrieving singleton_class/singleton_methods of object. etc.

####Kernel

Kernel is very special in the hierarchy, because it's absolutely not class but an object. On the other hand, Kernel is mixed into class Object, making it become the earliest members in the whole hierarchy and also play very important role in the process.

Unlike Object, Constants in Kernel do not refer to some parameters of internal Ruby lang, they're more like an utility or tool kit for subclasses, especially the reference to popular classes. Kernel also plays hard in Process operations(abort, fork, spawn), Source file loading(auto loading, require), Exception(catch, fail, raise, throw), String(chomp, chop, format, gets, gsub, sprintf, sub), Controlling(loop, sleep, rand, srand, trace/untrace), and IO(open, print, put, readlines, select).

Kernel also enhances the metaprogramming, like binding, block_given?, caller, eval, lambda, and proc.

###3. Modules and Classes

####Module

Basically, Modules serve two purposes in Ruby: namespacing and mix-in functionality.

A namespace can be used to organize code by package or functionality that separates common names from interference by other packages. Mix-in functionality allows sharing common methods across multiple classes or modules. Ruby comes with the Enumerable mix-in module which provides many enumeration methods based on the each method and Comparable allows comparison of objects based on the <=> comparison method.

A module is created using the module keyword.

####Class

Every class is also a module, but unlike modules a class may not be mixed-in to another module (or class). Like a module, a class can be used as a namespace. A class also inherits methods and constants from its superclass. Use the class keyword to create a class. Any method defined on a class is callable from its subclass, the same is true for constants. You can override the functionality of a superclass method by redefining the method. If you wish to invoke the superclass functionality from a method use super.

If you do not supply a superclass your new class will inherit from Object. You may inherit from a different class using < followed by a class name.

When used without any arguments super uses the arguments given to the subclass method. To send no arguments to the superclass method use super(). To send specific arguments to the superclass method provide them manually like super(2). super may be called as many times as you like in the subclass method.

####Common properties

Note that there are many similarities between modules and classes. Besides the ability to mix-in a module, the description of modules also applies to classes.

#####Reopening

Reopening classes is a very powerful feature of Ruby, but it is best to only reopen classes you own. Otherwise it may lead to naming conflicts or difficult to diagnose bugs.

#####Nesting

Modules may be nested.

#####Packaging

Many packages create a single outermost module (or class) to provide a namespace for their functionality. You may also define inner modules using :: provided the outer modules (or classes) are already defined.

#####Self

self refers to the object that defines the current scope. And it will change when entering a different method or when defining a new module.

#####Constants

Accessible constants are different depending on the module nesting (which syntax was used to define the module). if you use :: to define A::B without nesting it inside A a NameError exception will be raised because the nesting does not include A. If a constant is defined at the top-level you may preceded it with :: to reference it.

#####Methods

Class methods may be called directly(This is slightly confusing, but a method on a module is often called a “class method” instead of a “module method”. See also Module#module_function which can convert an instance method into a class method.). When a class method references a constant it uses the same rules as referencing it outside the method as the scope is the same.
Instance methods defined in a module are only callable when included. These methods have access to the constants defined when they were included through the ancestors list.

#####Visibility

Ruby has three types of visibility. The default is public. A public method may be called from any other object.

The second visibility is protected. When calling a protected method the sender must be a subclass of the receiver or the receiver must be a subclass of the sender. Otherwise a NoMethodError will be raised. Protected visibility is most frequently used to define == and other comparison methods where the author does not wish to expose an object's state to any caller and would like to restrict it only to inherited classes.

The third visibility is private. A private method may not be called with a receiver, not even self. If a private method is called with a receiver a NoMethodError will be raised.

#####Alias and Undef

You may also alias or undefine methods, but these operations are not restricted to modules or classes.

###4. Singleton classes

The singleton class (also known as the metaclass or eigenclass) of an object is a class that holds methods for only that instance. You can access the singleton class of an object using class << object like this:

```ruby
class Foo
  class << self
    # self is the singleton class here
  end
end

class << Foo
  # self is the singleton class here
end
```

This allows definition of methods and attributes on a class (or module) without needing to write def self.my_method.

Since you can open the singleton class of any object this means that this code block:

```ruby
bar = Object.new

def bar.bar_method
  1 + 1
end
```

is equivalent to this code block:
```ruby
bar = Object.new

class << bar
  def bar_method
    1 + 1
  end
end
```

Understanding singleton class in ruby is very important to investigate internal ruby, especially for its OO design. In next article, we'll introduce the scope mechanism in ruby.

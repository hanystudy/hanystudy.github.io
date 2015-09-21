---
layout: post
title: "metaprogramming ruby: core concepts - scope &amp; self"
date: 2015-09-08 13:47:47 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

Scope defines the circumstance around any ruby statement, which basically limits your accessibility to resources. But ruby also has implicit and complicate Constants/Methods lookup strategy to make things not so easy at all. This article will clarify the concepts about scope and self firstly, then deep into the Constants/Methods lookup strategy. Finally, let's try to understand top-level which may be the most special case in this topic.

###1. Scope

In most cases, the 'Scope' we say in ruby commonly refers to variable scope, which has a little bit different from other popular languages. For example, there are four basic variable scopes including local, global, instance and class level, none of them has business with each other. We've already know the naming specification below:

|Name begins with|Variable scope|
|:--------------:|:------------:|
|[a-z] or _|local variable   |
|$         |global variable  |
|@         |instance variable|
|@@        |class variable   |

Actually the tricky is not naming, it's about default scope changing. As we know that ruby has lexical scope, but not like others, it does not support nested scope, like inner scope shares outer scope in some languages. Each of four variable scopes in above figure is purely independent to each other.

#### message & scope resolution operator
Dot . is message operator in ruby, mainly used for message receiving, like method call. Double colon :: is scope resolution operator, commonly used for getting constant in specific scope, Like Foo::Bar, means searching ./foo/bar with relative path. If Foo is undefined in current scope, you should always use ::Foo::Bar to figure it out using absolute path.

#### scope changing

There are four basic cases which may create and change the scope lexically: Class definitions (class), Module definitions (module), Methods (def) and Blocks. Block scope is a little bit different from others and more interesting, which would create its own scope after the block defined, but also have closure capability, like code below:

```ruby
foo = ''
bar = lambda { foo = 'hi' if foo == 'hello' }
foo = 'hello'
bar.call
foo #hi
```

For sure we can break this very easily:

```ruby
foo = ''
bar = lambda { |;foo| foo = 'hi' if foo == 'hello' }
foo = 'hello'
bar.call
foo #hello
```

Notice that |;foo| defines foo as a local variable in its block, this kind of feature was introduced from ruby 1.9.

#### constants scope

The scope we've talked until now basically only means variable scope. In ruby, constant is totally different type of data compared with other languages like C++ or Java, any constant name must be started with uppercase character. And none of them can be assigned dynamically. Like:

```ruby
def foo
  Bar = 'hello'
end #should give SyntaxError for dynamic constant assignment
```

Constants defined within a class or module can be accessed within its parent. And scope operator we talked before should be used when it's outside the class or module. Constants defined outside any class or module are global scope, can be accessed directly or by using the scope operator '::' with no prefix.

###2. Self

As we know that the grail in ruby world would be conciseness. However, such conciseness is built on many bright solutions like keyword self.

if you just type a name in ruby console, it will show you an error:

```ruby
foo #NameError: undefined local variable or method `foo' for main:Object
```

Indeed it has a huge gap between ruby and other languages in such case. But the expression with only name as a statement in ruby really means asking for local variable first, if found nothing then go on for searching method defined by current object. Here 'main' is a special object which represents the top-level, will have more discussion in the last part of this article.

#### calling methods

When you want to call a method, there should always be a receiver pointed to target object. Commonly that would be in this form:

```ruby
some_object.some_method
```

If some_object is omitted, some_method will be called on the object where the current scope belongs to. self is one of the keywords referring to the current object. Note that private is an exception in this case, we will show an example here which has already been introduced in last several articles talking about private and public.

```ruby
def foo
  'Hello'
end

foo #Hello
self.foo #NoMethodError: private method `foo' called for main:Object
```

The most usage of self would be defining a class method, or getting singleton class of current class.

###3. Method lookup

The order of name lookup should be local variable/constant, then method. We has discussed about variable and constant scope in the first part. Here let's have a look at method lookup.

When a message call happens, ruby will found its receiver firstly which should be an object. Since all methods are stored in classes and modules so method lookup walks these, not the objects themselves.

Here is the order of method lookup for the receiver’s class or module R:

####i. The prepended modules of R in reverse order.

####ii. For a matching method in R.

####iii. The included modules of R in reverse order.

If R is a class with a superclass, this is repeated with R‘s superclass until a method is found. And once a match is found method lookup stops. If no match is found this repeats from the beginning, but looking for method_missing. The default method_missing is BasicObject#method_missing which raises a NameError when invoked.

###4. Understanding top-level

May be you're not interested in top-level mechanism as a rails developer (You have to use top-level one day even you're only develop rails application), but you need to notice that ruby is also regarded as a powerful scripting language and already as a built-in tool in many popular OS distributions. We talk about top-level because it behaves different from any ruby official documents.

I've found a nice explanation about top-level [here](https://banisterfiend.wordpress.com/2010/11/23/what-is-the-ruby-top-level/), but there are some newer update in latest ruby, we'll go through this.

Most of us may know that top-level refers to main, which is an object of Object. You can prove it very easily like this:

```ruby
self #main
self.class #Object
```

But how about define a top-level method?

```ruby
def foo
  'hello'
end
foo #hello
method(:foo).owner #Object
Object.private_method_defined?(:foo) #true
self.class.private_instance_methods(false) #[:foo]
```

Does that look like a class definition? Even for the constant definition:

```ruby
Foo = 'hello'
Object.const_defined?(:Foo, false) #true
```

It just looks like you're operating on class Object. Not only for this, we found that there can also use public, private, or include method, they are shadow in singleton_class of main:

```ruby
singleton_class.private_instance_methods(false) #[:public, :private, :include, :using, :define_method]
```

Based on our analyst above, we find a strange dual-nature of ruby top-level with some unexpected definitions in main object in purpose. Matz gives a [explanation](http://groups.google.com/group/comp.lang.ruby/browse_thread/thread/7bb7b451a8f3cca7/98c4c62127b9d945) about design of top-level. You can also get more discussion [here](http://stackoverflow.com/questions/1761148/where-are-methods-defined-at-the-ruby-top-level). From my view, such unusual design exactly helps conciseness of this language, but also gains our confusion and curiosity.

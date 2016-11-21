---
layout: post
title: "metaprogramming ruby: core concepts - extensions"
date: 2015-11-03 16:08:28 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

As we already know, ruby provides several ways to extend our classes and objects including but not limit to OO inheritance, Module mix-in, Dynamic Methods, Monkey patching / Refinement, Eval family, Ghost methods, Callable objects, Singleton classes / methods, etc. But there are still some important tips deserve to be mentioned, before we moving forward.

### 1. Include vs Extend

Firstly let’s see an example:

```ruby
module Foo
  def self.foo
    p 'Hello world'
  end

  def say_hi
    p 'Hello world'
  end
end

class Bar
  include Foo
end

Bar.foo #NoMethodError: undefined method `foo' for Bar:Class
Bar.new.say_hi #Hello world
```

It seems that module mix-in can only involve instance methods but not others like class methods. But how to do similar thing at class level? You must hear about extend:

```ruby
module Foo
  def foo
    p 'Hello world'
  end
end

class Bar
  extend Foo
end

Bar.foo #Hello world
```

Extend is used in object level, so we are sure it can also be used for any class in ruby. But in previous article, we know that class method is actually saved as a singleton method of the original class, also instance method of its singleton class. So that should also happen on include:

```ruby
module Foo
  def foo
    p 'Hello world'
  end
end

class Bar
  class << self
    include Foo
  end
end

Bar.foo #Hello world
```

Thus we can regard extend as a kind of special usage on include through above examples.

### 2. Method wrappers

Method wrapper means wrapping an existing method inside a new method, which is very useful when you want to make extension without changing the source, like code in standard library or other cases.

There are several ways to implement method wrappers in ruby, and they are all in composite form of primitives which’ve already been introduced in previous articles. We’ll go through below.

#### Around Alias

Module#alias_method (also the key word ‘alias’) is used to give another name to ruby methods. It will involve more accessibility if an usual method could have different domain names(e.g. size, length and count). Also more flexibilities if you want, like code below:

```ruby
class Foo
  def say_hi
    p 'Hello'
  end
end

class Foo
  alias_method :say_hello, :say_hi

  def say_hi
    say_hello
    p 'World'
  end
end

foo = Foo.new
foo.say_hi #Hello\nWorld\n
```

This is just a kind of wrapper using open class, alias_method, and method redefinition.

#### Refinement

We talked about refinement and suggested using refine instead of monkey patch. Actually refinement is even more powerful than that.

```ruby
class Foo
  def say_hi
    p 'Hello'
  end
end

module FooRefinement
  refine Foo do
    def say_hi
      super
      p 'World'
    end
  end
end

using FooRefinement

Foo.new.say_hi #Hello\nWorld\n
```

Only thing you need to notice is that the key word ‘using’ may not work well with your IRB environment, which means you couldn’t get result in mind for some versions of ruby if you run those code in IRB. See more information [here](https://bugs.ruby-lang.org/issues/9580).

The benefit of refinement wrapper is controllable scope of wrapper unlike around alias which affects globally. However, accessibility to original method is also lower than alias way.

#### Prepending

Module#prepend is the simplest way to implement method wrapper without scope configurability like refinement. But much more clear than other two.

```ruby
class Foo
  def say_hi
    p 'Hello'
  end
end

module FooPrepending
  def say_hi
    super
    p 'World'
  end
end

Foo.class_eval do
  prepend FooPrepending
end

Foo.new.say_hi #Hello\nWorld\n
```

### 3. Class Macros

Ruby objects have no attributes - May this won’t surprise or confuse you too much. Indeed we’ve hear about instance variables or class variables, but you can not access them directly from outside. That means getter or writer can not be avoided:

```ruby
class Foo
  def bar=(value)
    @bar = value
  end

  def bar
    @bar
  end
end

foo = Foo.new
foo.bar = 'Hello'
foo.bar #Hello
```

It’s just not ruby style! Ruby provides series accessors for class definition using metaprogramming api, like attr_accessor, attr_reader and attr_writer, they are quite intuitive to use:

```ruby
class Foo
  attr_accessor :bar
end

foo = Foo.new
foo.bar = 'Hello'
foo.bar #Hello
```

attr_* come from Module as private instance methods, thus they all can be used in module or class definitions.

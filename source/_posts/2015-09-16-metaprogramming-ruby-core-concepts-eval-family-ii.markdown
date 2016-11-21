---
layout: post
title: "metaprogramming ruby: core concepts - eval family II"
date: 2015-09-16 09:20:27 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

In previous article several basic scope controlling tools are introduced, like block, proc, lambda, and method object. All of them should work well when you organize them in your code reasonably. There are more flexible way to mix code and bindings in ruby - eval, which may not be used broadly than others, so that considerable traps about it are still unclear for us.

### 3. instance_eval / instance_exec

BasicObject#instance_eval can be used almost everywhere in ruby, you can use it like this:

```ruby
foo = Object.new
foo.instance_eval { @bar = 'hello' }
foo.instance_variable_get('@bar') #hello
```

In the block of instance_eval, the scope changes to the instance foo, thus any operations inner it should bind to the instance, except closure variables.

BasicObject#instance_exec has similar feature to eval one, but with arguments support. This benefit shows as below:

```ruby
class Foo
  def initialize
    @bar = 'hello'
  end
end
output = 'world'
foo = Foo.new
foo.instance_exec(output){|output| output = "#{@bar} #{output}"}
output #world
```

### 4. class_eval / class_exec

Module#class_eval works for evaluating a block in the context of an existing class. It seems to be similar to instance_eval which will have scope changes to instance self, but also changes current class (excluding singleton class).

class_eval can be used to open a existing class, without using keyword ‘class’. That means it has no scope changing issue compared with keyword way. Actually it even does not need to know the constant name of the target, while keyword way indeed needs.

Also Module#class_exec plays same role like instance_exec for instance_eval.

Think about the thing what we’ve discussed several weeks ago, in ruby all items are instance of class, including classes no matter internal or customized. Which means instance_eval and class_eval both can work for classes in many cases, but indeed have different self meaning.

You may also notice that there is also Module#module_eval / Module#module_exec methods, but they are just alias for class_eval / class_exec without any other changes.

### 5. Kernel#eval

Unlike instance_eval/class_eval, Kernel#eval only accept a string of ruby code as its argument, run the code and give the result. Even it’s so powerful, using string of code is not a good choice if you have other ways. The most issue for string of code is security.

Suppose you have eval expression in your code, which means others can evaluate any code using such method, including get/set operations. Ruby defines safe levels, which actually limits the evaluation of code from outside. By default, ruby will mark potentially unsafe objects which many come from external sources. And user could config a global variable $SAFE, it’s range from 0 to 3(0 by default) with more strict.

```ruby
$SAFE = 1
eval "p 'hello #{gets()}'"
world #SecurityError: Insecure operation - eval
```

By default, eval can accept almost any code from outside. However, it will not be permitted for code from I/O device(tainted object) if $SAFE is none zero. Below gives more details about $SAFE:

|$SAFE| Constraints|
|-------------|:-------------:|
|0|No checking of the use of externally supplied (tainted) data is performed. This is Ruby’s default mode.|
|>= 1|Ruby disallows the use of tainted data by potentially dangerous operations.|
|>= 2|Ruby prohibits the loading of program files from globally writable locations.|
|>= 3|All newly created objects are considered tainted.|

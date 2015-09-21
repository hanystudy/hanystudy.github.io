---
layout: post
title: "metaprogramming ruby: core concepts - eval family I"
date: 2015-09-14 23:08:19 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

In ruby, block is the cornerstone of metaprogramming with a surprisingly powerful scope controlling capability. Based on this definition, much brilliant interfaces are introduced, and help to implement many valuable features, like callable objects, eval and its huge family.

### 1. blocks

Block represents a snippet of code, it permits to be called immediately or lately depends on different cases. But compared with some similar concepts in functional programming languages, like Lisp, block still has more limits, and more readability respectively.

In commonly, block can be used like this:

```ruby
def show_hello
  yield 'hello' if block_given?
end

show_hello { |say| puts say }

show_hello do |say|
  file = File.open('/tmp/output.txt', 'w')
  file.write say
  file.close
end
```

'yield' is a ruby keyword used to call blocks sent to the current method. block_given? is an instance method from Kernel to probe whether there is a block for current method.

One of the most useful aspect of block is closure, it captures bindings where it's defined, and avoid impact of scope changing by connecting to those bindings, like flat scopes, and shared scopes.

Frankly speaking, block is actually not responsible for running code, but only representation (Except yield, which indeed means running code immediately). There are more powerful tools to help enhance block usage.

### 2. callable objects

Block is just like a package of code, and you need to use yield to execute it if you like. However, there are more ways to package code in ruby, including Proc and lambda.

#### Proc

We already know that block is not an object in ruby which is really quite a few, but Proc is basically a block turned object, can be seen as a consistent form of block, and you do not have to use yield to run it immediately, it will be running later as you want (Deferred Evaluation). you can define a Proc like this:

```ruby
inc = Proc.new {|x| x + 1}
inc.call(1) #2
dec = proc {|x| x - 1}
inc.call(2) #1
```

#### lambda

Except for Proc, lambda can also be used for transferring blocks, but with simpler and a little different way:

```ruby
inc = lambda {|x| x + 1}
inc.call(1) #2
dec = ->(x) {x - 1}
dec.call(2) #1
```

#### & operator

Once you have defined a block for a method, there is a way to convert it to a Proc inner method by using &. For example:

```ruby
def bar
  yield 'ruby'
end

def foo(&operation)
  bar(&operation)
  operation.call('world')
end

foo {|x| puts "hello #{x}"}
# hello ruby
# hello world
```

& can be seen like transferring a block into Proc, but you need to remove & if you want to use it as a Proc.

#### Comparison between procs and lambdas

You may notice that lambda is also a Proc, but you can still use Proc#lambda? to see the actual type of the target. Besides there are two important differences: keyword 'return' and arguments checking.

##### a. return

As plain blocks, the program will return out of scope where block defined if return statement is set inner block, so does Proc (which may mean that the return may cause exception when the block is called in nonreturnable scope, like top-level). While for lambda, the return only runs out of block, not even farther.

##### b. arity

Arity means the number of arguments. In real case, lambda has less tolerance than Proc, which means that lambda requires correct number of arity, which is no need for procs.

#### Method objects

All methods are objects of Method. You can use Kernel#method or Kernel#singleton_method to get the object of method, then use Method#call to run the method. Also you may use Method#to_proc and define_method to convert method and proc to each other. Eventhough the method still has scope of it's defined.

##### Unbound Methods

Method object can also be unbound from original scopes by using Method#unbind, or Module#instance_method. Generated UnboundMethod object can not be called directly, but you can still call it after bind it to another scope by using UnboundMethod#bind. The only notice is that UnboundMethod object can only be bound to same class or sub if it's from class, and no such limitation when it's from module.

---
layout: post
title: "metaprogramming ruby: core concepts - BasicObject"
date: 2015-08-03 07:33:27 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

Metaprogramming is one of core features for ruby lang, it's widely used from internal ruby to standard library, also endless magic gems. In next a few articles we will focus on those core stuff. I recommend for [Metaprogramming ruby 2](https://pragprog.com/book/ppmetr2/metaprogramming-ruby-2) as a reference, except a little verbose and may be only for primer usage. For more ascent knowledge, perhaps you need to track the [trunk](https://github.com/ruby/ruby) of ruby. This essay will concentrate on BasicObject, which is parent for all classes in ruby.

### BasicObject
As the object oriented hierarchy in ruby core, each type of class is also object of Class except BasicObject, and All the objects of Class has root superclass BasicObject, the one is also object of Class. Thus we'll have whole picture below.

![Ruby OO hierarchy](http://7xk84n.com1.z0.glb.clouddn.com/metaprogramming_ruby/ruby_class.png)

#### i.class methods
BasicObject only has single public class method, names "new".

#### ii.instance methods
logical operators, any instance of BasicObject is almost empty, with only a few methods, like logical operators !, !=, ==. Here == has same meaning with equal? for object level equality, The difference is that == is recommended for descendant overriding, but equal? should always be same as it is defined in BasicObject.

##### \_\_id\_\_ / object_id
An integer identifier of an object, should be unique for a given object. But some objects of builtin classes are reused for optimization, like immediate values (not passed by reference but value, like nil, true, false, Fixnums, Symbols, and some Floats) and frozen string literals.

##### \_\_send\_\_ / send
Double underscore gives a backup for pure alphabet version which may be overrode through other ways. Invokes the method, and passes all arguments using dynamic number of arguments.

##### instance_eval / instance_exec
instance_eval support passing a string of ruby source code, with file path and line number when compilation errors happen, also given block, within the context of receiver. Thus eval code will have access to instance variables and private methods.

instance_exec only support using block as an argument, but allows one more argument to be passed from outer scope into receiver.

#### iii.private instance methods

##### method_missing

In BasicObject, method_missing belongs to private instance methods, any call for this method will raise an error. But descendant can override this method to do dynamic processing, according to user's input like symbol name and arguments. If overrode method does not want to do any processing, super should be called to pass message bottom from up.

##### singleton_method_added / singleton_method_removed / singleton_method_undefined

Callback for singleton method operations. As we already know, ruby will not create the whole clone of the class for any objects. Only with a middle layer between object and class - singleton class. Singleton class is generated when object comes out, and any messages sent to object should be transferred to its singleton class first, then its own class if not matches in singleton class.

class's singleton class is different to object's singleton class. That's because singleton class of a class should be inherited from its parent's singleton class, until BasicObject's singleton class, then it can reach non singleton class. But for object, singleton class should always have parent class of its own class.

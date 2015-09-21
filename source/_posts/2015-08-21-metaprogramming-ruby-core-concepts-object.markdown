---
layout: post
title: "metaprogramming ruby: core concepts - Object"
date: 2015-08-21 11:24:33 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

As the default root of all Rubric objects, Object holds quite a lot valuable instance methods for common usage. It mixes in the Kernel module to provide global accessibility of Kernel APIs for all inherited classes. This article will introduce Object definition.

### Object

Object only holds a few constants and instance methods for objects of descendants, all these methods are only used for instance level metaprogramming.

#####class
Gets class Constant of object.

#####define_singleton_method / new_method
Defines a singleton method in the receiver. The parameters can be a Proc, a Method or an UnboundMethod object. If a block is specified, it is used as the method body.

#####extend
Enhance object with instance methods from modules given as a parameter.

#####instance_of?
Check if obj is an instance of the given class(Except ancestors).

#####instance_variable_defined?
Check if the given instance variable is defined in obj.

#####instance_variable_get
Gets the value of the given instance variable, or nil if the instance variable is not set. Just note that the @ part of the variable name should be included for regular instance variables.

#####instance_variable_set
Sets the instance variable named by symbol to the given object. The variable does not have to exist prior to this call.

#####instance_variables
Returns array of instance variables

#####is_a? / kind_of?
Returns true if class is the class of object, or if class is one of the superclasses of obj or modules included in obj.

#####itself
returns object itself

#####method
Looks up the named method as a receiver in object, returning a Method object. The Method object acts as a closure in object's object instance, so instance variables and the value of self remain available.

#####methods
Returns a list of the names of public and protected methods of object. This will include all the methods accessible in obj's ancestors. If the optional parameter is false, it returns an array of object's public and protected singleton methods, the array will not include methods in modules included in object.

#####private_methods
Returns the list of private methods accessible to object.

#####protected_methods
Returns the list of protected methods accessible to object.

#####public_method
Similar to method, searches public method only.

#####public_methods
Returns the list of public methods accessible to obj.

#####public_send
Invokes the method identified by symbol, passing it any arguments specified. Unlike send, #public_send calls public methods only.

#####remove_instance_variable
Removes the named instance variable from object, returning that variable's value.

#####respond_to_missing?
Hook method to return whether the object can respond to id method or not.

#####send / \_\_send\_\_
Invokes the method identified by symbol, passing it any arguments specified. You can use \_\_send\_\_ if the name send clashes with an existing method in obj.

#####singleton_class
Returns the singleton class of object. This method creates a new singleton class if obj does not have one. If object is nil, true, or false, it returns NilClass, TrueClass, or FalseClass, respectively. If object is a Fixnum or a Symbol, it raises a TypeError.

#####singleton_method
Similar to #method, searches singleton method only.

#####singleton_methods
Returns an array of the names of singleton methods for object. Only public and protected singleton methods are returned.

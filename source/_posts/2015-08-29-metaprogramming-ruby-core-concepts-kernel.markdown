---
layout: post
title: "metaprogramming ruby: core concepts - Kernel"
date: 2015-08-29 21:57:31 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

The Kernel is the object of Module, thus it has all instance methods from the parent. Also it's available as instance methods in every Ruby object since the Kernel is included by class Object. Kernel has widespread usage including but not limit to internal Constant references, Processes operations, Loading Strategies, Exceptions, Controlling, IO, and Basic string operations. This article will focus on metaprogramming on instance level.

###Kernel

All Constants and instance methods are public in Kernel, thus they all can be used in any objects as callee.

#####binding
Returns a Binding object, describing the variable and method bindings at the point of call. This object can be used when calling eval to execute the evaluated command in this environment.  

#####eval
Evaluates the Ruby expression(s) in string. If the binding is given, which must be a Binding object, the evaluation is performed in its context. If the optional filename and lineno parameters are present, they will be used when reporting syntax errors.

#####block_given? / iterator?
Returns true if yield would execute a block in the current context. The iterator? form is mildly deprecated.

#####callcc{|cont| block}
Generates a Continuation object, which it passes to the associated block. You need to require 'continuation' before using this method. Performing a cont.call will cause the callcc to return (as will falling through the end of the block). The value returned by the callcc is the value of the block, or the value passed to cont.call.
In general, Continuation object is used to be analogous to C setjmp/longjmp, or a thread. cont will return itself, and cont.call will jump to end of callcc block.

#####caller
Returns the current execution stack—an array containing strings in the form file:line or file:line: in 'method'. Returns nil if start is greater than the size of current execution stack. Optionally you can pass a range, which will return an array containing the entries within the specified range.

#####caller_locations
Returns the current execution stack—an array containing backtrace location objects. Returns nil if start is greater than the size of current execution stack. Optionally you can pass a range, which will return an array containing the entries within the specified range.

#####eval
Evaluates the Ruby expression(s) in string. If binding is given, which must be a Binding object, the evaluation is performed in its context. If the optional filename and lineno parameters are present, they will be used when reporting syntax errors.

#####global_variables
Lookup global variables.

#####lambda
Equivalent to Proc.new, except the resulting Proc objects check the number of parameters passed when called.

#####proc
Equivalent to Proc.new

---
layout: post
title: "metaprogramming ruby: core concepts - Module"
date: 2015-08-26 23:18:08 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

For internal ruby, Module is mainly used for holding class methods for descendants. Suppose each object has class of A, A always has class of Class, and Class inherits from Module, thus class A should hold all class methods from instance methods in Module. On the other hand, user can mix in self defined Module to provide more instance methods for their class, or use module function directly.

###Module

Module provides two kinds of class level metaprogramming methods, either public or private. Each of them has different usage for building descendants.

####Public instance methods

#####ancestors
Returns a list of modules included/prepended in current module.

#####class_eval
Evaluates the string or block in the context of module, except that when a block is given, constant/class variable lookup is not affected. This can be used to add methods to a class. class_eval returns the result of evaluating its argument.

#####class_exec
Evaluates the given block in the context of the class/module. The method defined in the block will belong to the receiver. Any arguments passed to the method will be passed to the block. This can be used if the block needs to access instance variables.

#####class_variable_defined?
Returns true if the given class variable is defined in object.

#####class_variable_get
Returns the value of the given class variable (or throws a NameError exception). The @@ part of the variable name should be included for regular class variables.

#####class_variable_set
Sets the class variable named by symbol to the given object.

#####class_variables
Returns an array of the names of class variables in current module. This includes the names of class variables in any included modules, unless the inherit parameter is set to false.

#####const_defined?
Says whether module or its ancestors have a constant with the given name: If module is a Module, additionally Object and its ancestors are checked. In each of the checked classes or modules, if the constant is not present but there is an autoload for it, true is returned directly without autoloading. If the constant is not found the callback const_missing is not called and the method returns false. If inherit is false, the lookup only checks the constants in the receiver. In this case, the same logic for autoloading applies. If the argument is not a valid constant name a NameError is raised with the message “wrong constant name name”.

#####const_get
Checks for a constant with the given name in module. If inherit is set, the lookup will also search the ancestors (and Object if module is a Module). The value of the constant is returned if a definition is found, otherwise a NameError is raised. This method will recursively look up constant names if a namespaced class name is provided.

#####const_missing
Invoked when a reference is made to an undefined constant in module. It is passed a symbol for the undefined constant, and returns a value to be used for that constant. If found, it returns the loaded class. It therefore implements an autoload feature similar to Kernel#autoload and #autoload.

#####const_set
Sets the named constant to the given object, returning that object. Creates a new constant if no constant with the given name previously existed. If symbol or string is not a valid constant name a NameError will be raised with a warning “wrong constant name”.

#####constants
Returns an array of the names of the constants accessible in module. This includes the names of constants in any included modules, unless the inherit parameter is set to false.

#####instance_method
Returns an UnboundMethod representing the given instance method in module.

#####instance_methods
Returns an array containing the names of the public and protected instance methods in the receiver. For a module, these are the public and protected methods; for a class, they are the instance (not singleton) methods.

#####method_defined?
Returns true if the named method is defined by module (or its included modules and, if module is a class, its ancestors). Public and protected methods are matched. String arguments are converted to symbols.

#####module_eval
Evaluates the string or block in the context of module, except that when a block is given, constant/class variable lookup is not affected. This can be used to add methods to a class. module_eval returns the result of evaluating its argument.

#####module_exec
Evaluates the given block in the context of the class/module. The method defined in the block will belong to the receiver. Any arguments passed to the method will be passed to the block. This can be used if the block needs to access instance variables.

#####name
Returns the name of the module module. Returns nil for anonymous modules.

#####prepend
Invokes Module.prepend_features on each parameter in reverse order.

#####private_class_method
Makes existing class methods private. Often used to hide the default constructor new.

#####private_constant
Makes a list of existing constants private.

#####private_instance_methods
Returns a list of the private instance methods defined in module. If the optional parameter is false, the methods of any ancestors are not included.

#####private_method_defined?
Returns true if the named private method is defined by \_mod\_ (or its included modules and, if module is a class, its ancestors).

#####protected_instance_methods
Returns a list of the protected instance methods defined in module. If the optional parameter is false, the methods of any ancestors are not included.

#####protected_method_defined?
Returns true if the named protected method is defined by module (or its included modules and, if module is a class, its ancestors).

#####public_class_method
Makes a list of existing class methods public.

#####public_constant
Makes a list of existing constants public.

#####public_instance_method
Similar to instance_method, searches public method only.

#####public_instance_methods
Returns a list of the public instance methods defined in module. If the optional parameter is false, the methods of any ancestors are not included.

#####public_method_defined?
Returns true if the named public method is defined by module (or its included modules and, if module is a class, its ancestors).

#####remove_class_variable
Removes the definition of the symbol, returning that constant's value.

#####singleton_class?
Returns true if module is a singleton class or false if it is an ordinary class or module.

####Private instance methods

Private instance methods in Module are mainly used in internal classes level.

#####alias_method
Makes new_name a new copy of the method old_name. This can be used to retain access to methods that are overridden.

#####append_features
When this module is included in another, Ruby calls append_features in this module, passing it the receiving module in module. Ruby's default implementation is to add the constants, methods, and module variables of this module to module if this module has not already been added to module or one of its ancestors.

#####define_method
Defines an instance method in the receiver. The method parameter can be a Proc, a Method or an UnboundMethod object. If a block is specified, it is used as the method body. This block is evaluated using instance_eval, a point that is tricky to demonstrate because define_method is private.

#####extend_object
Extends the specified object by adding this module's constants and methods (which are added as singleton methods). This is the callback method used by Object#extend.

#####extended
The equivalent of included, but for extended modules.

#####included
Callback invoked whenever the receiver is included in another module or class. This should be used in preference to Module.append_features if your code wants to perform some action when a module is included in another.

#####method_added
Invoked as a callback whenever an instance method is added to the receiver.

#####method_removed
Invoked as a callback whenever an instance method is removed from the receiver.

#####method_undefined
Invoked as a callback whenever an instance method undefined but called from the receiver.

#####module_function
Creates module functions for the named methods. These functions may be called with the module as a receiver, and also become available as instance methods to classes that mix in the module. Module functions are copies of the original, and so may be changed independently. The instance-method versions are made private. If used with no arguments, subsequently defined methods become module functions. String arguments are converted to symbols.
prepend_features When this module is prepended in another, Ruby calls prepend_features in this module, passing it the receiving module in module. Ruby's default implementation is to overlay the constants, methods, and module variables of this module to mod if this module has not already been added to mod or one of its ancestors. See also Module#prepend.

#####prepended
The equivalent of included, but for prepended modules.

#####refine
Refine klass in the receiver. Returns an overlaid module.

#####remove_const
Removes the definition of the given constant, returning that constant's previous value. If that constant referred to a module, this will not change that module's name and can lead to confusion.
remove_method Removes the method identified by symbol from the current class. For an example, see Module.undef_method.

#####undef_method
Prevents the current class from responding to calls to the named method. Contrast this with remove_method, which deletes the method from the particular class; Ruby will still search superclasses and mixed-in modules for a possible receiver. String arguments are converted to symbols.

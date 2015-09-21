---
layout: post
title: "metaprogramming ruby: core concepts - open classes &amp; refinements"
date: 2015-09-08 22:40:23 +0800
comments: true
categories:
- Ruby
tags:
- Ruby
- 元编程
---

### Opening Classes

Opening classes brings much flexibility for ruby. It permits you to modify existed classes or modules permanently, without having to implement whole world in case you just need to give a simple patch for current tool. For example:

```ruby
class String
  def to_alphanumeric
    gsub(/[^\w\s]/, '')
  end
end
```

to_alphanumeric is new interface to return only alpha and numeric part of a string. However, opening class may not work well, especially it's commonly dangerous to opening a frequently used target which would cause unexpected error in code. Due to this reason someone calls opening classes monkey patch and just leaves far from this feature.

While whatever, monkey patch continues conciseness in ruby, sometimes you are able to save your life gracefully with such powerful tool, except the trap behind it.

### Refinements

From ruby 2.0, there is a more advanced form of monkey patch which is called refinements. Refinements are providing a way to extend classes only under specific scope, but not including modules. For example:

```ruby
class C
  def foo
    puts "C#foo"
  end
end

module M
  refine C do
    def foo
      puts "C#foo in M"
    end
  end
end
```

Module#refine creates an anonymous module that contains the changes or refinements to the class (C in the example). self in the refine block is this anonymous module similar to Module#module_eval.

#### Scope

We use using to activate refinements defined in any places, but you may only activate refinements at top-level, not inside any class, module or method scope. You may activate refinements in a string passed to Kernel#eval that is evaluated at top-level. Refinements are active until the end of the file or the end of the eval string, respectively.

Refinements are lexical in scope. When control is transferred outside the scope the refinement is deactivated. This means that when you require or load a file or call a method that is defined outside the current scope the refinement will be deactivated.

If a method is defined in a scope where a refinement is active the refinement will be active when the method is called.

When defining multiple refinements in the same module, inside a refine block all refinements from the same module are active when a refined method is called.

You may also activate refinements in a class or module definition, in which case the refinements are activated from the point where using is called to the end of the class or module definition.

#### Method lookup under refinement

When looking up a method for an instance of class C Ruby checks:

##### 1. If refinements are active for C, in the reverse order they were activated:

  The prepended modules from the refinement for C

  The refinement for C

  The included modules from the refinement for C

##### 2. The prepended modules of C

##### 3. C

##### 4. The included modules of C

If no method was found at any point this repeats with the superclass of C.

Note that methods in a subclass have priority over refinements in a superclass. For example, if the method / is defined in a refinement for Integer 1 / 2 invokes the original Fixnum#/ because Fixnum is a subclass of Integer and is searched before the refinements for the superclass Integer.

If a method foo is defined on Integer in a refinement, 1.foo invokes that method since foo does not exist on Fixnum.

#### Super

When super is invoked method lookup checks:

##### The included modules of the current class. Note that the current class may be a refinement.

##### If the current class is a refinement, the method lookup proceeds as in the Method Lookup section above.

##### If the current class has a direct superclass, the method proceeds as in the Method Lookup section above using the superclass.

Note that super in a method of a refinement invokes the method in the refined class even if there is another refinement which has been activated in the same context.

#### Refinements and module inclusion

Refinements are inherited by module inclusion. That is, using activates all refinements in the ancestors of the specified module. Refinements in a descendant have priority over refinements in an ancestor.

After all, refinement is still an experiment feature in ruby 2.x series, there is a detailed [specification](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/RefinementsSpec) in official website, which should cover most aspect of it.

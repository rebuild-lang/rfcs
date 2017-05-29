# Compile Time Execution and Syntax

- Created: 2016-05-08 by arBmind


## Summary
[Summary]: #summary

This RFC lays out the all considerations about compile time execution.

It proposes to use the ampersand prefix as syntax to mark blocks and functions to execute at compile time or to invoke regular functions at compile time inside a non-compile time block or function.


## Motivation
[Motivation]: #motivation

To allow the maximum possible user extensibility for the language we should enable compile time code execution.

Compile time execution allows to reflect and manipulate types and functions. It enables restrictions and custom checks.

This feature substitutes and extends C++ `constexpr` and a large part of templates.
We also want avoid any kind of macro language.


## Detailed design
[Detailed design]: #detailed-design

Instead of declaring only file and class blocks. We allow code anywhere.
This means everything is executed like a script.

Anything is allowed to execute.
The language itself does not impose restrictions.
The scripting runtime might impose rules for untrusted source code.

```rebuild
if rebuild.target.has_128bit_float
  type signed : extend = f128 # type is only extended if condition is met
end
```

Functions and blocks are natural barriers.
The body is not automatically executed at compile time.
We need syntax to make the invocation compile time automatically or to invoke a function at compile time.

This allows functions to add declarations to types and modules.

I propose the usage of the ampersand. It is no longer needed for references and addresses.

The operator can be used in the following scenarios. 

If it is used in a definition of a variable or type then the definition is only usable at compile time.

```rebuild
var &n = 5 # basically every use of n is replaced with 5
type &t = i32; # a variable of t will be compile time only.
```

Use in a function name. The function is executed at compile time in the callers scope. This allows do build types and implement functions.

```rebuild
fn &foo    # foo will always be invoked at compile time.
    type fooint = i32 # and defines fooint in the caller scope
end
```

If the ampersand is used in front of an expression, the expression is forced to evaluated at compile time. This allows basically to evaluate any function at compile time.

```rebuild
def sintable = &(makesintable(360)) # forced to calculate at compile time

&do # the entire block is executed at compile time
  type nestedint = i32 # nestedint lands in surrounding scope
end
```

In regular functions you may use the ampersand in front of argument names or types. In front of the name, it will trigger that only compile time constants can be assigned. In front of types, it makes the type name behave like an argument, that will be infered for each invocation.

```rebuild
fn foo (
    &arg : i32, # arg is a compile time constant
    list : &T # T is the compile time type of the list argument
    ) -> T
end
```

Compile time executed code has the option to interact with the compiler.
It may add link libraries, load dlls or compile other files.


## Drawbacks
[Drawbacks]: #drawbacks

By executing at the namespace/module scope we are limited to definition order.
We might try to hide this with deferred evaluations in regular code, but compile time code will be able to watch this.

I don't think this is such a huge deal. Programmers are humans and we normally read from top to bottom. We might consider to allow forward declarations if no other solutions exist. We should try do avoid this.

## Alternatives
[Alternatives]: #alternatives

### More involved syntax

For example `@run` or `compiletime`.

It may lead to more wordy syntax. But I think we should use a short syntax for such a powerful core feature and encourage everyone to learn and use it.

We might also use comments or annotations.

### Persistent markers in names

We might force the use of an `!` exclamation mark on every compile time name and it's usage.

This would mark every use of this feature. This inhibits the usage as an enabler for language extensions.

Users are free to mark compile time constructs. But it should not be mandatory.


## Unresolved questions
[Unresolved questions]: #unresolved-questions

### Should a regular function have the option to evaluate differently at compile time and runtime?

Yes, this might be important to support different DLLs or use different evaluation schemas.

I suggest something like a compiler constant `rebuild.compiletime`. It is true during compile time execution and false when real code is generated.


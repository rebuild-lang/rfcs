# Assignable and Mutable Markers

- Created: 2016-12-22 arBmind



## Summary
[Summary]: #summary

This proposal describes a language change to mark variables as assignable and types as mutable.


## Motivation
[Motivation]: #motivation

For a good system language, we have to distinguish pointers to mutable and immutable memory.
We should also be able to model pointers to pointers with all combinations of mutabilty.

Variables and arguments should be distinguished between read only and assignable variants. This allows the implementation of assignment operators that manipulate left side arguments.



## Detailed design
[Detailed design]: #detailed-design

If we want to design a flexible language, we need a way to model all kinds of arguments for functions.

`in` arguments, that are read only inside the function.
`out` arguments, that do not have to be initialized before calling the function.
`ref` arguments, that model a combination of the above.

If we think further ahead, we should model more states into the types. We want to express that a pointers target is read only. This will help to reuse buffers in all kinds of scenarios.

So far we planned to have separate `var` and `def` keywords to introduce modifiable variables or constants. The pitty is that this won't help in parameter declarations. Also it remains undefined what "modifiable" means exactly.

I suggest to only use the `let` keyword to introduce variables and constants. To distinguish the use cases we should add operator signs or context sensitive keywords. I favor the operator signs as these modifiers are used quite often.

Instead of introducing new sign we may use `&` and `*`.
`&` sign is used as a compile time markers.
`*` sign is used for pointers and dereferences.


### Mutable markers

The `&` sign is only used before an indentifier in declarations and before value expressions.
I propose to use it in type expressions to mark mutable types.

```rebuild
let a : T = () # a is of a non mutable type T
let b : &T = () # b is of a mutable type T
```

We can also use this with combinations of pointers.

```rebuild
let pa : *&T = () # pa is a pointer to a mutable T
let ppa : *&*&T = () # ppa is a pointer to a mutable pointer to mutable T
```

Without a mutable marker everything is immutable by default.
Initialisations remove the mutablility if it is not stated otherwise.
The `&` behind the identifier can be used to prevent this.

```rebuild
fn foo () -> &*&T
let r = foo # r is non mutable pointer to mutable T
let s& = foo # s is mutable pointer to mutable T 
```

This can also be extended to member funtions declarations.
Function declarations where the identifier is appended with an `&` will get a mutable flag. Blocks can use this to distinguish between self mutating member functions when generating the final type signature.


### Assignable markers

The `*` sign is only used in the type expression of an identifier declarations.
In parameter declarations it may be used to collect types out of a parameter block into another type.

I propose that adding a `*` before an identifier in the declaration marks it as assignable.

```rebuild
let *a = 4 # a is assignable with new values
a = 5 # allowed now 
```

This also allows to write the signature of an assignment operator.

```rebuild
fn (*left : u64) = (right : u64)
```

In function signatures it is equal to reference semantics of C++.


### Assignable vs. Mutable

At first it might seem redundant to have markers for both.

Assignable affects only variables and arguments. Assignable means we allow the variable or argument to be assigned a new value.

Mutable on the other hand means that the inner state of an type can be changed.

An Example:

A color object with values for red, green and blue.

You might mutate an existing color instance to a different red value.
Mutating might also involve changing all three values to create a brighter or darker color.

If we model the color as a value object, no mutations are allowed. Which might help us to cache colors. Changing a red value means that we create a new color.
If we model a pen and change the color, we assign a new color to it.

Having both concepts in a programming lanuage helps us to model exactly what we mean.


## Drawbacks
[Drawbacks]: #drawbacks

### This proposal might not be complete. We might have forgotten cases, that we later need to make ovious to the programmer.

I suggest that this RFC is changed or counter proposals are created when necessary.

### Using operators makes it not intuitive to learn.

As argued in the proposal, these constructs are used very often and should be short to write.
I believe if the concepts are transported well, the syntax is not an issue.

If we find out that my believes are wrong, we should try to go the keyword route. Some regular expression magic should be enough to transform existing code.



## Alternatives
[Alternatives]: #alternatives


### Use pointers to mark assignable arguments

My first try to sort this out, was to use pointers for assignable arguments.
It is very easy on the syntax but also not very expressive.

All raw pointers are writable. We have to wrap them in custom types like `const_ptr(T)`. But this is the wrong default. The concept that is easy to reason about should also be the easy to express.

The other issue is the call syntax. Basically all assignments would require a pointer on the left side. `*a = 5`. This becomes really tedious to write.


### Use some implicit logic for reference and value semantics

Some languages like Swift handle classes and integrated types differently. The goal might be the reduction of pointer usage. Pointers are more complex and marked unsafe.

Even though I do not like the magical change, they are quite successful with this. Rebuild does not provide the one and only way to classes. To implement this, we would need a way to distinguish classes and value types.

This alternative was not fully thought through. It might be a good idea to look into this.


## Unresolved questions
[Unresolved questions]: #unresolved-questions


### How does this behave in real use cases?

We have to try it an gather experience.


### How do we mark ownership transfers?

No proposals yet.

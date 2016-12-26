# Introduce Meta Types

- Created: 2016-04-03

## Summary
[Summary]: #summary

The user should be able to extend the type system with custom meta types and a consistent syntax.

## Motivation
[Motivation]: #motivation

Classical system languages have a very limited type system.

Even with operator overloading of C++ a class or struct cannot build all the necessary abstractions. For example to model strict value types or even actors.

This RFC proposes the introduction of a more flexible type system.

## Detailed design
[Detailed design]: #detailed-design

In C++ we know the following type categories:

- `basic` - bool, int, float (compiler & system provided value types)
- `enum` - user defined names for values
- `struct` & `class` - OOP structures

Instead of treating these special, they should be one of many. Everything is just a type and the compiler has to know how to deal with instances of them.

It might look like this:

- `type rgb : value …` - value type semantics for user defined types
- `type color : enum …` - list of values
- `type person : struct …` - for structured data declaration

This syntax is very consistent and keeps the number of keywords low.

We might want to provide more meta types in the language. Each should be discussed in all details in a seperate RFC.

- `type expression : union …` - a variant type that contains one of the types
- `type expression : actor …` - restricts access outside of the actor

## Drawbacks
[Drawbacks]: #drawbacks

### Default types need more letters to write.

I believe it's worth to have a very consistent syntax and to keep the keyword introduction to all declarations.

## Alternatives
[Alternatives]: #alternatives

We can keep the classical type system and try to make struct/class flexible enough to model the desired behaviors.
> I doubt this is possible.

It might also be possible to achieve the same goal with a clever macro system. 
> This will lead to some surprises for the developer. With the proposed syntax it is at least clear that we define a type and that the behavior is somewhat restrictet to the declared type.

Some behavior is achievable with template meta programming or something similar.
> The proposed system is more flexible. Template meta programming will need a very elaborative syntax, that makes it hard to get readable APIs.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

### Which meta types should be built in?

It is beyond the scope of this RFC. Each meta type should be proposed as a seperate RFC.

### How are user defined meta types defined?

No proposals exist so far. Please suggest something in a new RFC.

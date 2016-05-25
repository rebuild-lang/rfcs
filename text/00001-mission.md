# Rebuild mission

- Created: 2016-03-21

## Summary
[Summary]: #summary

Our mission is to rebuild system programming to be fun and productive again.

The most important goals:

- [Developer happiness]
- [Zero overhead]
- [User growable]

## Motivation
[Motivation]: #motivation

Without a mission we tend to make the same mistakes again or fall into new traps others managed to ignore. Only if we change our methods we will approach new worlds.

## Detailed design
[Detailed design]: #detailed-design

The Rebuild project simply aims to build the next generation programming language.

### Developer happiness
[Developer happiness]: #developer-happiness

Using the Rebuild language should be fun.

The language should be easy to learn, teach, write and read.

#### We should always maximize consistency in syntax and semantics.

The syntax has to be short but expressive enough to make it easy to read even if you dont know the language.

#### Aim to make the right things the easiest.

The most common use case should be the easiest to write. More special behaviors can be propotionally more elaborative. And even the most esoterical stuff should be doable with reasonable effort.

### Zero overhead
[Zero overhead]: #zero-overhead

All constructs should place no more time, memory and other resource overhead over the same feature in a handwritten assembly machine instructions.

This is the core statement of C, C++ and Rust. I believe any system language is required to do so.

We do not aim to build the next scripting language. There are enough of them. Only a hand full of system languages exist.

This means we should evaluate every feature to be the most resource effective among the alternatives.

### User growable
[User growable]: #user-growable

The language and standard library should be extendable by every user of it.

#### This should enable and encourage a faster evolution of the language.

With all the ideas on the table we should be able to make well-adviced decisions on what will be standardized.

## Drawbacks
[Drawbacks]: #drawbacks

A the beginning we have little material to show and discuss. This tends to turn people away, that need a solution now. I guess this is right and helpful to the project.

We might not find a consesus on anything substantial. Even this mission statement might get lost. I believe this is ok. If we have a healthy discussion and get every argument on the table, we have all the options to find solutions that work better than what any indiviual can achive. Maybe there is enough room to build two versions, if this helps two distinct audiences.


## Alternatives
[Alternatives]: #alternatives

Use and improve any of the existing programming languages.


## Unresolved questions
[Unresolved questions]: #unresolved-questions

Almost everythig is unresolved at the moment. That's why I started this project.

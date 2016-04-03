# Keyword initiated declarations

- Created: 2016-04-03

## Summary
[Summary]: #summary

We shoud go for keyword based declarations.

## Motivation
[Motivation]: #motivation

Inspired by Ruby, Swift and Rust a keyword initiated declaration is easier to recognize and read.

C and C++ syntax to declare a function is quite complicated to teach. It also limits the usabilty to situations where the compiler expects them.

## Detailed design
[Detailed design]: #detailed-design

All declarations should start with a keyword. These declarations are the only way to introduce a new concept to the compiler.

The declaring keywords generally only work at the beginning of a statement. They might have a different meaning in other positions.

Examples of declaring keywords:

- `fn`, `fun` or `func` - a function
- `type` - a type
- `let` - a constant
- `var` - a variable
- `module` or `namespace` - a named global scope

Each keyword is followed by the name of the declaration and is continued by a declaration specific stuff. Which is part of another proposal.

## Drawbacks
[Drawbacks]: #drawbacks

It might be a bit more to type. But it should be well worth the effort, to get a constistent and readable language.

## Alternatives
[Alternatives]: #alternatives

The C and C++ system of declarations. Where functions start with the return type and variables with the type.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

### Which declaration types are needed and which require their own keyword?

This question should be prosponed for now. It should be discussed in other RFCs.

### What keywords should be used for each declaration?

This question should be discussed in the respective RFCs.

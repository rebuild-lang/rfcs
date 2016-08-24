# Line Continuations

- Created: 2016-08-24 by @arBmind

## Summary
[Summary]: #summary

I propose a way to handle line continuations and expression separation without semicolons.

## Motivation
[Motivation]: #motivation

In order to get a very fluent language we should not require semicolons.
Semicolons are one of the hardest things to learn in a programming language.

## Detailed design
[Detailed design]: #detailed-design

Removing the requirement to place semicolons between all statements reduces a huge error potential for programmers.

It can be achieved through differenet strategies:
* Automatically place semicolons with rules like Javascript or Ruby does it.
    - This leads to issues if developers do not remember these rules.
* Use indentation to mark line continuations like Python does it.
    - This rule is very simple and very visual.

We also want to allow multiple lines of visually nested line continuations.

This should be valid.
```
line1
    continue1
       continue1a
    continue2
```
All lines are parsed as if it was one line, with whitespaces where the line breaks are.

It should be illegal to jump above it first continuation line.
```
line1
    continue1
  more # <= error
```
Visually this pattern is reserved for blocks.
The error should propose to start a block or indent the line correctly.
Internally the `more` starts a block and is added to the line continuation.
Later errors in parsing either of these are ignored.

## Drawbacks
[Drawbacks]: #drawbacks

### This introduces semantics to indentation

As every developer should indent these statements any way, so they become readable, I do not the a large issue here.

## Alternatives
[Alternatives]: #alternatives

### Add a continuation operator like shells

This would basically be the inverse rule to a semicolon.
It is also visually not pleasing and kind of hard to maintain.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

None so far.

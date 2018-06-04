# Line Continuations

- Created: 2016-08-24 by arBmind
- Updated: 2018-06-03 by arBmind

## Summary
[Summary]: #summary

This RFC proposes a way to handle epxression separation and continuations without the use of separator signs like semicolons.

## Motivation
[Motivation]: #motivation

In order to get a very fluent language we should not require semicolons.
Semicolons are one of the hardest things to learn in a programming language.

Removing the requirement to place semicolons between all statements reduces a huge error potential for programmers.

## Detailed design
[Detailed design]: #detailed-design

This paper proposes the use of indentation for line continuations.
This is a visual approach that is also used by Python.

Two lines with the same indentation are treated as two separate expressions.
```
line1
line2
```

If the second line is indented more, the expression started in the first line is continued in the second line.
```
line1
    line2
```

### Tabs or Spaces

Unlike Python we do not want to impose our own preference to every developer.

Both styles are allowed. But mixing them in one file is an error.

* Once the compiler has seen tabs for indentation, the use of spaces for indentations is an error.
* Vice versa, once Spaces were used for indentations, the use of tabs for indentations is an error.

### Nested Line Continuation

Nesting continuations deeper should be allowed. This will help to group arguments in complex calls.

This should be valid.
```
line1
    continue1
       continue1a
    continue2
```
All lines above are parsed as if it was one line, with whitespaces where the line breaks are.

### Error: Backed off continuations

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

As every developer should indent these statements any way, so they become readable. I do not the a large issue here.

### IDE support

IDEs have a hard time to help you do the right thing.
They basically do not know if a line should be continued or seperated.

A good IDE might use the same rules as automatic semicolon insertions and try to help the programmer to get it right. The big benefit here is that the solution is visible and errors can easily be fixed.


## Alternatives
[Alternatives]: #alternatives

### Expression delimter

As stated in the motivation, we want to avoid the noise of adding semicolons or any other delimiter.

### Automatically insert delimiters

We could define rules like Javascript and Ruby that insert semicolons automatically.

Even though this works fine most of the time. When it fails to do the right thing nobody seems to remember all the rules and edge cases.

### Continuation markers

This would basically be the inverse rule to a semicolon.
It is also visually not pleasing and kind of hard to maintain.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

None so far.

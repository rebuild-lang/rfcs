# Block Grouping

- Created: 2016-08-24 by @arBmind

## Summary
[Summary]: #summary

This proposal introduces a simple but powerful rule to allow multiple block arguments in a visually pleasing way.

## Motivation
[Motivation]: #motivation

No language I know does this. So let's collect some points.

If we want to give developers the power to develop methods like `if-then-elsif-else`, we need multiple block arguments.

## Detailed design
[Detailed design]: #detailed-design

The main goal is to allow visually pleasing way to pass multiple block arguments to a statement.

Ruby allows one block argument.
```ruby
[1,2,3].each do |x|
  print x
end
```
This is both very cool but also limiting to a single block.

We have to be able to always extract the block arguments for an expression, because the parsing of the block content will happen in a different scope.

My initial idea was to use curly braces for blocks like in C based languages.
```
if condition {
    # ...
} else {
    # ...
}
```
Now we exactly know where a block starts and ends.

But we still do not know where the `if` arguments end.
The `else` might be placed on the next line. To know if this is part of the `if` arguments, we would need to know the declaration of `if`. Which we might not know because it is inside a block itself.

Therefor my proposal is to go for the colon `:` pattern.
```rebuild
if condition:
  # true block
elsif condition:
  # elsif true block
else:
  # else block
end
```
The `:` is always placed last on a line. Only whitespaces and comments are allowed after it.
The `end` frames the whole invocation, no matter how many block arguments are involved.

Again this rule is very simple, but very effective.
Unlike Python with the expected `end` statement, we can catch most indentation errors.


```rebuild
if condition:
  # true code
# end - missing

if condition:
  # more code
end
```
If the line after the missing end has no colon, the grouping breaks with an error.
The missing `end` is inserted to continue parsing.

If the next line has a colon, no grouping error is recognized.
Most likely the invocation resolution will fail.
If it passes we have no chance to distinguish this from correct code.


## Drawbacks
[Drawbacks]: #drawbacks

### A missing `end` might lead to wrong grouping and strange behavior.

No perfect workaround is possible.

### Multiple block arguments may lead to hard to read code.

This might be true.
But it's in the hands of the developer to use this feature to write maintainable code.
Not allowing this will hinder good use cases as well.

## Alternatives
[Alternatives]: #alternatives

So far no good alternatives are known or discussed.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

### Are further arguments allowed after `end`?

Generally the visibility is hindered, so we should disallow it until we find a reasonable use case.
Ending the arguments after `end` with another `:` colon, would be very strange.

### How are block associated to function arguments?

This topic should be discussed in a separate RFC.



# Block Grouping

- Created: 2016-08-24 by arBmind
- Updated: 2018-06-03 by arBmind

## Summary
[Summary]: #summary

This proposal introduces a simple but powerful rule to allow multiple block arguments in a visually pleasing way.


## Motivation
[Motivation]: #motivation

Rebuild aims to give the developer almost the same power as a language designer.

If we want to give developers the power to develop methods like Ruby's `if-elsif-else`, we need to support multiple blocks as arguments.


## Detailed design
[Detailed design]: #detailed-design

The Ruby language allows one block argument.
```ruby
[1,2,3].each do |x|
  print x
end
```
These blocks in Ruby is very useful. But the developer is still limited to easily passing a single block to a function call.

### Block argument syntax

This proposal introduces the following syntax:

- Each block starts with a colon `:` at the end of the line. Whitespaces and comments after the colon are ignored.
- The body of each block is indented and ends with first unindented line.
- An unindented line begins with the name of an argument or the `end` marker.
- After an unindented argument name the value of the argument is expected.
- An expression with blocks ends with the first unindented `end` marker.

This would look like this:
```rebuild
if condition:
  # true block
elsif condition:
  # elsif true block
else:
  # else block
end
```

Please note that after `end` marker no further arguments are allowed.

The recognition of blocks shall be done in the 'block grouping' filter pass without any context.

### Error handling

With the rules for unindented lines and the required `end` marker, we should able to catch and recover most indentation errors.
```rebuild
if condition:
  # true code
# end - missing

if condition:
  # more code
end
```

If the line after the missing end has no colon, the grouping breaks with an error. The missing `end` is inserted to continue parsing.

If the next line has a colon, the grouping error is recognized by the parser should fail in recognizing the argument. A special handling should be implemented to recognize this pattern when parsing failed.

There is still a possibilty that the parser succeeds unintentionally. These situations should be very rare.


## Drawbacks
[Drawbacks]: #drawbacks

### A missing `end` might lead to wrong grouping and strange behavior

We acknowledge that the error recovery is not provable. But the same applies to all alternatives and also to existing languages.

### No identifier named `end` allowed

The identifier `end` is not prohibited. Misindenting this identifier would lead to some confusion. But we might add some logic to disect the resulting error messages.

Arguments that contain blocks that are named `end` might be unusable, because they cannot be named. That is a price we will have to pay for this.

### Multiple block arguments may lead to hard to read code

It is always in the hands of the developer to write maintainable code.
Not allowing this will hinder good use cases as well.


## Alternatives
[Alternatives]: #alternatives

### C Style curly braces

The initial idea was to use curly braces for blocks like in C based languages.
```
if condition {
    # ...
} else {
    # ...
}
```

Using curly braces the start and end of every block is very well defined.

But we run into issues with associating the `else` to the `if` call.
Basically we would have three options:

1. We require semicolons after the last block.
2. `} else {` is required to be a one-liner.
3. The parser is required to know and look for `else`.

All of these options have significant drawbacks and were therefore discarded.

1. Semicolons everywhere is very consistent but would look strange.
2. Takes away a lot of styling freedom and is unseen in any C style language.
3. In this case possible, but with multiple overloads this would escalate to a parsing nightmare.


## Unresolved questions
[Unresolved questions]: #unresolved-questions

### Are further arguments allowed after `end`?

Generally the visibility is hindered, so we should disallow it until we find a reasonable use case.
Ending the arguments after `end` with another `:` colon, would be very strange.

### How are arguments passed to the block?

Blocks themselves have no arguments.
The usage of blocks in Ruby is quite confusing. They are somehow a mixture of a lambda and a real code block.

The blocks we use here behave like in control flow statements.
Each block has it's own sub-scope.

Adding arguments to block might be discussed in a later RFC.

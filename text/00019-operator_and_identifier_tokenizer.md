# Operator and Identifier Tokenizer

- Created: 2016-08-24 by @arBmind

## Summary
[Summary]: #summary

This proposal discusses the introduction and implication of custom operator identifiers.
It aims to allow 

## Motivation
[Motivation]: #motivation

To get started with parsing the language, we need to clarify how identifiers are treated.

Allowing the library authors to define custom operators makes the language much more generic.
This allows for great extensibility and innovative new operator concepts.

## Detailed design
[Detailed design]: #detailed-design

The goals:
* allow everything we can
* allow custom operators

Previous languages are very restrictive on operators and identifiers.
Scala takes a little more generic approach. Custom operators are allowed, as long as they start with a default operator sign. And normal identifiers may be used infix with backticks.

The most generic way is to allow all characters in identifiers and operators.
This is difficult to parse, as we can only know of the expression tokens, when the scope it known.
`a+b` may mean all kinds of things. `a + b` is probably the most expected variant.

Even when allowing every character, we might want to apply some rules:
* Opening and closing punctuations like parentheses have to match.
* Some character sequences have fixed meaning for the language. like comments, keywords, commas etc.
* Spaces always separate identifier sequences
* Keywords and identifiers arguments have to be clearly separated by the tokenizer. 

The tokenizer cannot separate all the identifiers.
This task has to be delayed until the parser has figured out the scope for the block.
Otherwise some identifiers might be missing.
Then the longest known identifier is used, starting at the beginning.

If we know `a`, `b` and `+` then `a+b` is splitted into identifiers `a`, `+` and `b`.
If an additional identifier `a+` is introduced, the split will change to `a+` and `b`, which might lead to issues.
This is not that bad for a binary operator, because most often we want spaces around them anyways.
It's more difficult for unary operators like `++`, where the code may stay valid but behave different.

In order to cope with this I propose to introduce a separation of character sets.
* Regular identifiers are made of letters, numbers and underscores.
* Operator identifiers are made of symbols, emojis and punctuations that are not taken by the language itself.

Open and closing punctuations always have to match. This allows to place regular identifier characters in between. `{{` is not a valid operator, as the curly braces are not closed.

Some valid operator examples:
* Unicode.MathSymbols: `+` `=` `-`
* Unicode.OtherSymbol: `®` `⌛` `⌚`
* Unicode.OtherNumber: `½` `²`
* Unicode.CurrencySymbol: `¢` `¥` (`$` is reserved for the language)
* Unicode.OtherPunctuation: `?` `!` (`#,.` are reserved for the language)
* Unicode.OpenPunctuaton: `{dotproduct}`
* Unicode.InitialQuotePunctuation: `«cross»`

With this `a+b` is always parsed into three identifiers.
All operators are still subject to splitting the longest known operator, but regular identifiers are not.

There is no restrictions where you use what.
Variables may use the operator characters, but it should be avoided except for callable variables.
For example we want to allow a function that get's two operators as arguments.

```rebuild
&fn madd(l : $L, m : $M, a : $A, fn(:L)*(:M)->:$X, fn(:X)+(:A)->:$R) -> (r:R):
  r = (l * m) + a
end
```

## Drawbacks
[Drawbacks]: #drawbacks

### Compile Time

To split operators at least to passes are required.
One of the tokenizer and one to split tokens.

This is an inherent issue of user defined operators, that do not require split signs.
By splitting regular identifiers and operators the workload is reduced and most expressions do not have operators that require splitting.
Large sequences of operators are difficult to read by programmers.

### Surprises by introducing new operators

When a new operator is defined the splitting of operator sequences might change, without intention of the developer.

This is an inconvienience the developer has to cope with.
I guess for existing code basis you should be very careful to introduce new operators.

We might support this by allowing to import operators into a block or scope.
For example you have a matrix library with custom operators.
You would not want to introduce these operators everywhere, but only in a restricted scope.

## Alternatives
[Alternatives]: #alternatives

### Do not use custom operators

This leads to the issue that code with math is very difficult to read an maintain.

All operators have to be built into the language itself.

Many functional languages define a lot of operators for all kinds of function composition.
I propose that we do not burden every user of the language to learn them all.
But keep the option to use a library, that defines the operators the developer is willing to use.

### Mark operators

We might begin and end all custom operators with special characters.

This leads to very awkward looking code and is not very productive.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

### How is the parsing of expressions handled?

### How can the local import of operators be modeled?

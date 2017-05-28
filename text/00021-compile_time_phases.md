# Compile Time Phases

- Created: 2017-05-27, arBmind

## Summary
[Summary]: #summary

This request for comments introduces the idea of distinct compile time phases to enable and structure custom optimizations.

## Motivation
[Motivation]: #motivation

To allow the user of the language to add optimizations on all levels of the compilation, we have to divide the process into phases.
The optimizations may need a specific representation of the code, use statistical data for heuristics or have to ensure a type is always used right.
We also want to enable new kinds of optimizations, so developers can easily experiment and compare them.
And finally we want to allow user controlled optimizers and validators. This way a critical pixel loop may get more optimization effort than the argument parser.


## Detailed design
[Detailed design]: #detailed-design

### Background

Current compiler architecture from 10000 foot perspective use a three phase design. (eg. clang, gcc)

  `Frontend -> Optimizations -> Backend`

Internally the frontend for most languages has multiple passes. Passes are conceptionally separated but way work in locksteps.

1. Tokenizer : produces a stream of tokens
2. Parser : consumes tokens to an abstract syntax tree (AST)
3. Validations, Enrichment, Optimizations of the AST
4. Codegen : from AST to intermediate representation (IR)

The optimization phase does one or multiple attempts to optimize the IR as much as desired and possible.

The backend will do the register assignments and tries to generate the optimal assembly code for the target processor and the final result.

In order to create the most user extendable and fast system language, we have to model those passes as well. This will allow code to reflect over the entire application and apply the desired optimizations.

### Phases

This RFC suggests the introduction of a "phase" system, that stops any further processing until it is directly triggered.

A phase is a cooperation between the compiler and user extendable code representations.

The compiler provides knowledge about functions in modules. It knows the arguments declarations, allows annotations and invocations.

The code representation for each phase is determined by phase method invocations and restrictions to the types.

We will need a default base set of phases in the standard library. Developers can build optimizations and validations on top of these, or create new phases if they need to.

#### Phase 1. Expression-Tree

This phase is basically the result of the parser. This will include all the compile time code execution.

The declaration of the phase may look like this:

```rebuild
phase EXPR:
  fn (l) * (r) -> (v); # typed for all native types
  fn (l) + (r) -> (v);
  fn (&l) = (r) -> (&l);
  fn if (cond, then, else) -> (r);
  # …
end
```

The functions on a phase are a natural barrier. They do not require an implementation and are not executed directly.

All the arguments are expressions itself, thus building an expression tree. This distinguishes this phase from all later phases, where only constants and variables are allowed as arguments.

Implicitly all the user types, function and variable declarations and definitions are also preserved in this expression tree. How this is expressed in detail is currently not fully settled.

The representation of this phase allows basically all the AST optimizations, verifications of the pass 3 of the frontend.

Additionally it allows for formula manipulations.
It allows to target different expressions to different processing units.
In C++ you would need expression templates to model this.

Validations for pure functions, const variables can be inferred and checked quite easily on this level.

#### Phase 2. Intermediate Representations

This phase roughly models the final pass of the frontend.
The expression tree is flattened into one instruction sequence per function.

The instructions that are generated here, should represent the capabilities of the target platform. This includes operating system primitives and hardware computing operations.

The transformation should be done entirely through the means of compile time code execution. The compiler APIs will support the process with builtin AST walkers and instruction sequence builders. The schema is quite similar to regular compilers.

This phase may be skipped and the static single assignment (SSA) form is generated directly.

On the other hand this phase may allow other kinds of optimizations and are hard to do in SSA form.

#### Phase 3. Static Single Assignments

Once the IR is in SSA form the majority of optimizations should be applied.

The code can customize the optimizations with different annotations. This should allow us to optimize the critical paths much better than C++.

#### Phase 4. Assembly

When all the SSA based optimizations are done, the code is transformed into the target assembly language. This transformation involves register and stack assignments.

#### Phase 5. Packaging

All the methods are linearized into one or multiple linear address spaces. All code and data is aligned properly and finally the target file format is generated.

This phase will generate either host pages for immediate execution or executable files for delivery and later execution with an operating system or directly from the bootloader.

### Controlling phases

In order to allow customization to these phases and invocations to validations and transformations this is all written in code.

This might look like this:

```rebuild
&rebuild.compiler.run:
  var c = compile_to_phase EXPR
  c.add_file "main.rebuild"
  # … more files

  var phaseEXPR = c.build
  check_purity *phaseEXPR
  optimize_matrix_operations *phaseEXPR
  # … more checks & optimizations

  var phaseIR = buildIR phaseEXPR
  # …

  if isWindows:
    data = packagePE phaseASM
    rebuild.file.overwrite "example.exe" data
  end
end
```

The API can and should be improved. This code should only give an idea of how this may works.

### Debugging symbols

The phase APIs have to support the tracing of debugging symbols. Depending on the platform these debugging symbols may be packaged into the final executable or stored as separate debugging files.

### Fast Compile time execution

In order to get fast compile times, we need a way to just in time compile code that is executed at compile time. Because this should involve the same phases that require compile time execution, we need also a way to run those.

Rebuild will require a compile time interpretation of the compile phases to bootstrap the compilation of those phases.


## Drawbacks
[Drawbacks]: #drawbacks

The compiler requires a big stack of code to demonstrate that this concept works.

> I guess we cannot really do much about this. Normal compilers are quite complicated as well.
>
> We should build the stack with minimal effort. Creating the first phase representation is the biggest challenge.
> For demonstration purposes we might directly go from EXPR to ASM and do the packaging and only support a very minimal set of actions.
>
> I believe the possible benefits outweigh the effort to try this.


## Alternatives
[Alternatives]: #alternatives

1. We might implement the phases directly into the compiler.
2. We might allow plugins to allow custom optimizations and validations.
3. These plugins might be written in the Rebuild language.

> If we allow plugins, these require us to expose most of the compiler internals anyway. Changing those internals will most certainly break at least some plugins.
>
> Using the proposed approach of this RCF we might have more upfront costs, but it will be quite easy to keep the compiler APIs stable, because these are kept very small.

## Unresolved questions
[Unresolved questions]: #unresolved-questions

We do not know if the performance of this setup is sufficient.

> Unfortunately we will have to implement it to find out if it really is.

How does the reflection & transformation at each compile phase work?

> This is beyond of the scope of this RFC. We might want to conduct some experiments to find the best methods

Can we abandon and free the results of previous phases?

> We might want this, to reduce the memory pressure during the compilation and I don't know what should prevent this.
> The details of this are still unclear and should be decided later.

NOTE: This is work in progress

# const functions and methods for Dart

## Definition

A `const` function/method (CFM) is a function/method whose result can be used as
a fragment of a `const` expression.

## Declaration syntax

A CFM is declared like a normal function/method and:

* has `const` modifier prior to function/method signature
* its body is a `const` expression

CFMs may accept parameters, however, the same restrictions apply as those
imposed on the _const constructors_.

## Call sites

Because CFMs are also normal functions they may be used in non-`const`
expressions. In `const` expressions, however, the following restrictions are
imposed:

* (both) their arguments must be `const` expressions
* (methods only) they may only be called on a `const` value

## Semantics

A call to a `const` function in a `const` expression is semantically equivalent
to inlining the function into the expression.
==================================== 80 ========================================

NOTE: This is work in progress

# DEP: const functions and methods for Dart

## Goal

Introduce syntax for `const` function/method (CFM). CFM is:

* a function/method whose result can be used as a fragment of a `const`
  expression.

## Non-goals

Proposed is simple syntactic sugar on top of the existing `const`
expressions. `const` expressions can be syntactically and lexically
transformed to the existing syntax that is accepted by current Dart
implementations. Anything that requires more than static transformation
is beyond the scope of this proposal. For example, this proposal does
not include:

* control flow in `const` expressions
* lazy evaluation

## Syntax

A CFM is declared like a normal function/method and:

* has `const` modifier prior to function/method signature
* its body is a `const` expression

CFMs may accept parameters, however, the same restrictions apply as those
imposed on the _const constructors_.

## Call sites

Because CFMs are also normal functions they may be used in non-`const`
expressions. In `const` expressions, however, the following restrictions are
imposed (both means applied to both functions and methods):

* (both) their arguments must be `const` expressions
* (both) must be preceded by `const` (see also
  [optional const proposal](https://github.com/lrhn/dep-const/blob/master/proposal.md))
* (methods only) they may only be called on a `const` value (and therefore may
  only be defined on classes with `const` constructors)

## Motivating examples

Here's a very simplified
"[fluent](http://en.wikipedia.org/wiki/Fluent_interface)"
binding API for Angular 2 dependency injection:

```dart
class Binding {
  final Type type;
  final dynamic value;

  const Binding(this.type, this.value);
}

class BindingBuilder {
  final Type type;

  /// Note that class declaring `const` method is itself `const`.
  const BindingBuilder(this.type);

  /// A const method.
  ///
  /// Body of a `const` method is a `const` expression.
  const toValue(value) => const Binding(
    this.type,  // ok to refer to `this` as `this` is itself const
    value  // parameters are enforced to be `const` so can also be used
  );
}

/// A const function.
///
/// Body of a `const` function is a `const` expression.
const bind(Type t) => const BindingBuilder(t);

void main() {
  const binding = const bind(String).toValue('hello, world!');
}

// In Angular 2 apps, this fluent syntax may be used to declare bindings in
// component annotations, e.g.:
@Component(
  injector: const [
    const bind(String).toValue('hello, world!')
  ]
)
class MyComponent {
}
```

## Semantics

A call to a `const` function in a `const` expression is semantically equivalent
to static inlining the function into the expression.

### Example

```dart
// the example expression from above:
const bind(String).toValue('hello, world!');

// is unwrapped by inlining functions and methods:

// Step 1: inline `bind`
const BindingBuilder(String).toValue('hello, world!');

// Step 2: inline `toValue`
const Binding(String, 'hello, world!');
```

Note that the final unwrapped version of the expression is compiant with the
existing language spec.

## Optional const

With optional `const` proposed
[here](https://github.com/lrhn/dep-const/blob/master/proposal.md)
the syntax could be improved further:

```dart
@Component(
  injector: [
    bind(String).toValue('hello, world!')
  ]
)
class MyComponent {
}
```

<!--- ============================ 80 chars ================================ -->

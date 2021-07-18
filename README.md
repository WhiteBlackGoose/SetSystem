# Set System

Most programming languages today are type-based, that is, any value or variable has some type. It might
be dynamic or static typed, but the principle of types remains.

The problems with typed systems are constraints. It is hard to impossible to constrain a type to some
particular range of values (for example, limit string to be only consisting of digits) or vice versa,
expand the type to a range of values (int and strings). Some languages offer discriminated unions
and dependent types, but they might not fulfill our needs completely.

Hence, here we suggest a system based on sets. A value or expression doesn't have a type. But it may or
may not belong to some sets. Functions have domains, on which they must be fully defined. However, if the
caller of the function has not proven that their input belongs to the function's domain, the program does
not compile.

It will let us write exception-less and error-less code in almost natural mathematic language.

## Basic concepts

For every *value* `a` and *set* `b` it is either true or false that `a in b`.

Everything is a value.

Set is a value.

Function is a value.

Function is a set.

Function maps elements from the domain to the range. It is only possible to pass a value which belongs to the domain, otherwise, it is invalid program and does not execute.

A literal has no type, but it may be interpreted as an element from a set

## Values

A value is an expression or a literal:
```fs
let a = 1
```
or
```fs
let a = add 1 3
```
There's no type, instead, it's some expression.

## Sets

We can unite values:
```fs
let set A = 1 | 2 | '3'
```
We can use set builder notation:
```fs
let set B = all i from A where i in Ints
```
Finally, we can unite and intersect sets:
```fs
let set C = (A \/ B) /\ (1 | 3 | A)
```

## Functions

A function has a domain and a range, here's its declaration:
```fs
let map add: Ints x Ints -> Ints
```
Here's its definition:
```fs
        add a b = a + b
```

## Higher-order sets (data structures)

This may be a convenient way to work with sets too.

### Tuples

`Tuple` is an ordered set. `Tuple` of N elements is an element of Cartesian product
of corresponding elements' supersets. For example, the following is true:
```fs
(1, 2) in Ints x Ints
```
This is also true:
```fs
let set A = { 1 }
let set B = { 2 }
(1, 2) in A x B
```

### Strings

String of elemenets of alphabet A is a an element of the set `StringsOf(A)`. For an element `s` from
`StringsOf(A)` one of the following is true:
1. It is empty (denoted as `[]`)
2. There exists `c in A` such that the string is a tuple of `c` and `h` (TODO) for some `h in StringsOf(A)`.

## Example 1: parsing from string

A set of digits (typeless literals) in chars:

```fs
let set DigitsPrintedToChars = '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
```

A set of ints which we can parse from a string:

```fs
let set IntsPrintedToStrings = all i from Strings 
                               where i is c::s 
                               provided c in DigitsPrintedToChars and s is [] or in IntsPrintedToStrings
```

Where `Strings` is a built-in set of all possible seqeunces of characters.

Here's how we define the sets of characters which look like digits:
```fs
let map ParseChar: DigitsPrintedToChars -> Ints
        ParseChar c = { ('0', 0), ('1', 1), ('2', 2), ('3', 3), ('4', 4), ('5', 5), ('6', 6), ('7', 7), ('8', 8), ('9', 9) }
```

It's the pure mathematical definition of function as a set of binary relations. It maps a set of ten elements to another
set of ten elements. In other languages the closest principle would be `match` or `switch`.

```fs
let map Parse: IntsPrintedToStrings -> Ints
        Parse s = s is
            c::[] -> ParseChar(c)
            c::s -> ParseChar(c) * 10 + Parse s
```

Now, let's try an example:

```fs
let a = Parse "abc"
```

The code above does not compile, because `"abs"` does not belong to the domain of `Parse`.

And this code compiles:
```fs
let a = Parse "123"
```
and returns an element of range, which is `Ints`. Note that range is also a set => is a value.

`Parse`'s domain is a subset of `Strings` (by definition), so if we want to parse an arbitrary string, we
must prove that it belongs to that subset.

## Example 2: set madness

The code:
```fs
let set NonEmptySets = all u from U where exists e in U: e in u

// randomness cannot exist, so we need a determinant for our "random" function
let map GetRandomSet: NonEmptySets -> NonEmptySets
        GetRandomSet i = ...

let map Something: (GetRandomSet GetRandomSet GetRandomSet) -> GetRandomSet
        Something a = (GetRandomSet, GetRandomSet)
```

Explanation. This code is pretty self-explanatory, it finds all non-empty existing sets. Not clear though how we can
easily check the conditions of `exists e in U` though.
```fs
let set NonEmptySets = all u from U where exists e in U: e in u
```

```fs
// randomness cannot exist, so we need a determinant for our "random" function
let map GetRandomSet: NonEmptySets -> NonEmptySets
        GetRandomSet i = ...
```
That's a function which takes a non-empty set as an argument and maps it to another non-empty set. For now
we don't care about the relation ("implementation").

The most interesting part:
```fs
let map Something: (GetRandomSet GetRandomSet) -> GetRandomSet
        Something a = GetRandomSet a
```
`GetRandomSet` maps a non-empty set to a non-empty set. As an argument it got a `GetRandomSet` which *is* a non-empty set, because
`GetRandomSet` is a function and the set of non-empty sets is not empty (are we to prove it?).

Now, the only value it returns is the value of the only argument mapped through `GetRandomSet`.


## Runtime

Sets are unions or conditions so far, hence, it will be a hashset for unions and functor for conditions (maybe).


# Set System

This repo is about ideas for a set-based programming languages. Set-based as opposed to type-based.

## Basic concepts

For every *value* `a` and *set* `b` it is either true or false that `a in b`.

Everything is a value.

Set is a value.

Function is a value.

Function is a set.

Function maps elements from the domain to the range. It is only possible to pass a value which belongs to the domain, otherwise, it is invalid program and does not execute.

A literal has no type, but it may be interpreted as an element from a set

## Examples

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

## Runtime

Not thought yet.

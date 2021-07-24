## Questions to be decided

### Operators

Should they be defined only for some fixed sets, for instance,
```fs
let map +: Ints x Ints -> Ints
```

Or should they be defined on broader sets, like,
```fs
let map +: Addable -> AddableRange
```
where `Addable = Reals x Reals \/ Strings x Strings \/ ...`

But then we lose the strictness of sets: `+` always returns an element of `Ints` for `Ints x Ints`
input, which is not fixed for the declaration above.

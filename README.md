<div align="center">

# Zirco design docs

</div>

This document is part of the Zirco programming language's design documentation.

Zirco is a multi-paridigm general-purpose compiled programming language designed with type safety and readability as first priority.

The following is a quick overview of Zr's syntax:

```
// single-line comment
/* multi
   line
   comment /* with nesting */ */

// declares a function add with two integer parameters that returns an integer
fn add(int a, int b): int {
    return a + b;
}

// shorthand function declaration with '->', automatically return an expression
fn sub(int a, int b): int -> a - b;
```

## The `using` directive

```
using 'io'; // builtin modules do not require an "as" suffix, io:: namespace
using 'io' as foo; // foo:: namespace
using * from 'io'; // no namespace
using { a } from 'io'; // no namespace, only a
using { a as b } from 'io'; // no namespace. rename a to b
```

With local file paths, an `as` or `from` directive is required.

## Defined types

- `int` - integer
- `T[]` - dynamic size array
- `string` - dynamic size string
- `auto` - soon to be assumed types


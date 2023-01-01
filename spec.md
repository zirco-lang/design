<div align="center">

# Zirco specification (v0.1.0)

</div>

This document describes the official specification for the Zirco programming language.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][rfc2119].

The majority of the overall structure of this document was adapted from RFC 2616 "Hypertext Transfer Protocol -- HTTP/1.1", which can be found [here][rfc2616].

## Table of Contents

- [`1`](#-1) **Introduction**
  - [`1.1`](#-1-1) **Purpose**
  - [`1.2`](#-1-2) **Requirements**
  - [`1.3`](#-1-3) **Terminology**
- [`2`](#-2) **Notational Conventions**
  - [`2.1`](#-2-1) **Augmented BNF**
  - [`2.2`](#-2-2) **Basic Rules**
  - [`2.3`](#-2-3) **Versioning This Document**
- [`3`](#-3) **Basic Format**
  - [`3.1`](#-3-1) **Comments**
  - [`3.2`](#-3-2) **Statements and Expressions**
  - [`3.3`](#-3-3) **Identifier Format**
  - [`3.4`](#-3-4) **Program Structure**

<a id="-1">

## `1` **Introduction**

<a id="-1-1">

### `1.1` **Purpose**

Zirco is a multi-purpose multi-paradigm programming language. It is designed to be easy to learn and use, while also being powerful and flexible.

Zirco is designed with type-safety as a first class citizen.

<a id="-1-2">

### `1.2` **Requirements**

An implementation is considered "not compliant" if it fails to meet one or more of the MUST, MUST NOT, REQUIRED, SHALL, or SHALL NOT requirements in this specification, as described in [RFC 2119][rfc2119].

<a id="-1-3">

### `1.3` **Terminology**

The following terminology is used throughout this specification:

- **Program** - Any assembled collection of multiple Zirco source files
- **Module** - A single Zirco source file

<a id="-2">

## `2` **Notational Conventions**

<a id="-2-1">

### `2.1` **Augmented BNF**

The syntax of Zirco is defined using Augmented Backus-Naur Form (ABNF) as defined in [RFC 5234][rfc5234].

As with [RFC 2616][rfc2616], linear whitespace is used as follows:

> The grammar described by this specification is word-based. Except
> where noted otherwise, linear white space (LWS) can be included
> between any two adjacent words (token or quoted-string), and
> between adjacent words and separators, without changing the
> interpretation of a field. At least one delimiter (LWS and/or
> separators) MUST exist between any two tokens (for the definition
> of "token" below), since they would otherwise be interpreted as a
> single token.

<a id="-2-2">

### `2.2` **Basic Rules**

The following ABNF rules are used throughout this specification:

```
lchar   = %x61-7A       ; lowercase characters 
uchar   = %x41-5A       ; uppercase characters
char    = lchar / uchar ; any single a-z A-Z character
digit   = %x30-39       ; any single 0-9 character
lf      = %x0A          ; line feed
cr      = %x0D          ; carriage return
newline = [cr] lf       ; LF or CRLF
sp      = %x20          ; space
ht      = %x09          ; horizontal tab
<">     = %x22          ; double quote
```

To describe LWS (linear whitespace), the following rule is used:

```
LWS = [CRLF] 1*( SP / HT )
```

Between all word tokens, `*LWS` is implied.

<a id="-2-3">

### `2.3` **Versioning This Document**

This specification is versioned in a format similar to SemVer (Semantic Versioning) with a 3-component version identifier in the format X.Y.Z.

- **X** represents a major version or language change known to break existing programs.
- **Y** represents a minor version or feature added to the language without a breaking change.
- **Z** represents a "patch" -- grammatical fixes in this document.

```
version = 1*DIGIT "." 1*DIGIT "." 1*DIGIT
```

<a id="-3">

## `3` **Basic Format**

<a id="-3-1">

### `3.1` **Comments**

Comments SHALL be ignored by the compiler and are not part of the program.

Comments MUST be delimeted "C-style," with `//` for single-line comments and `/* comment */` for multi-line comments. Block comments may be nested.

```
line-comment = "//" *( %x20-10FFFF ) newline
block-comment = "/*" *( block-comment / %x20-10FFFF ) "*/"
```

Comments MAY be used at any place in a program -- including to separate tokens. With that in mind, block comments MUST count as linear whitespace as listed above.

```
LWS /= block-comment
```

<a id="-3-2">

### `3.2` **Statements and Expressions**

Any Zirco program consists of two main components: statements and expressions.

A "statement" says something and has no value. An "expression" returns one value.

For example, a declaration is a statement, while a function call is an expression.

All expressions and statements that are within a block but not within another expression MUST be terminated by a semicolon (`;`).

```
io::println(
  2 + 2 // not needed
); // needed

if (2 + 2 === 4) {
  io::println("2 + 2 is 4"); // needed
} // not needed
```

<a id="-3-3">

### `3.3` **Identifier Format**

An **identifier** is a sequence of characters that identifies a variable, function, or type.

Identifiers MUST start with a letter, an underscore, or a dollar sign, and MAY contain letters, numbers, dollar signs, and underscores (`_`).

```
identifier = ( char / "_" / "$" ) *( char / digit / "_" / "$" )
```

<a id="-3-4">

### `3.4` **Program Structure**

Zirco programs MUST consist of a `main` function. This `main` function is the entry point of the program and MUST return an `int`. The function `main()` MUST be defined in the global scope and receives one argument of type `string[]` representing the arguments passed at the command line (equivalent to C `char **argv` and Java `String[] args`).

```
fn main(string[]): int;
```


[rfc2119]: https://www.rfc-editor.org/rfc/rfc2119
[rfc2616]: https://www.rfc-editor.org/rfc/rfc2616
[rfc5234]: https://www.rfc-editor.org/rfc/rfc5234
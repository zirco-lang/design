<div align="center">

# Zirco specification (v0.1.0)

</div>

This document is versioned with SemVer such that the major version represents serious language changes, minor represents features, and patch represents typos/clarifications.

## Standards Used in This Document

This document uses a slightly augmented form of [Backus-Naur Form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form). The following section describes the format of a grammar:

1. Rules are described in the format `rule-name = value`.
2. Semicolons denote comments.
3. Rules can be referenced by name with or without the `<>` around them.
4. Strings are case-sensitive and use `""` as delimiters.
5. All tokens have implied word boundaries between them.
6. Direct hexadecimal character codes can be referred to with `%xAA` where `AA` is the hexadecimal code.
7. Multiple character codes can be described as `%xAA.BB` for `%xAA %xBB` and ranges use `%xAA-BB`.
8. The `/` character denotes a choice between multiple options.
9. Items may be grouped with `()`.
10. Optional tokens are obtained by following the token with `?`
11. Any number of a token is done by following the token with `*`.
12. One or more of a token is done by following the token with `+`.
13. Specific repetitions are implemented by following the token with `{MIN,MAX}`. MAX can be left empty to represent infinity. Min cannot be omitted and the comma is required.
14. Ranges of characters can be made using `[A-B]` using RegExp-like ranges.

## Base Rules

The following rules are defined now for our convenience:

```
CR         = %x0D
LF         = %x0A
NEWLINE    = CR? LF
CONTINUOUS = %x00-09 / %x0B-x0C / %x0E-10FFFF
ALL-TEXT   = %x00-10FFFF
WHITESPACE = " " / "\t" / NEWLINE
```

We also define `EOF` as the end of a file.

## Comments

In Zirco, comments may be nested. This comes with the definition of:

```
line-comment  = "//" CONTINUOUS NEWLINE
block-comment = "/*" (block-comment / ALL-TEXT) "*/"
comment       = line-comment / block-comment
```

All items matching `comment` are ignored.

## Operator Precedence

All Zirco operators use the following precedence table, where lower is less:

| Precedence | Name                        | Tokens                       | Associativity |
| ---------- | --------------------------- | ---------------------------- | ------------- |
| 1          | Postfix Increment/Decrement | `++` `--`                    | Left-to-right |
| 1          | Function call               | `()`                         | Left-to-right |
| 1          | Array access                | `[]`                         | Left-to-right |
| 1          | Object access               | `.`                          | Left-to-right |
| 2          | Prefix Increment/Decrement  | `++` `--`                    | Right-to-left |
| 2          | Unary minus                 | `-`                          | Right-to-left |
| 2          | Logical NOT                 | `!`                          | Right-to-left |
| 3          | Factor                      | `%` `/` `*`                  | Left-to-right |
| 4          | Term                        | `+` `-`                      | Left-to-right |
| 5          | Comparison                  | `<=` `<` `>` `>=`            | Left-to-right |
| 6          | Equality                    | `==` `!=`                    | Left-to-right |
| 7          | Logical AND/OR              | `&&` (two vertical bars that i cannot place here without breaking the table)          | Left-to-right |
| 8          | Assignments                 | `=` `+=` `-=` `*=` `/=` `%=` | Right-to-left |
| 9          | Comma                       | `,`                          | Left-to-right |

## Lexical Grammar

This grammar defines things the Lexer handles with tokens.

```
DIGIT      = [0-9]ESCAPED-
PREFIX     = "0b" / "0x"
NUMBER     = PREFIX? DIGIT+ ("." DIGIT+)?
ALPHA      = [a-zA-Z]
IDENTIFIER = (ALPHA / "_") (ALPHA / DIGIT / "_")*
STRING     = "\"" <any char except for "\"" without a "\\" prefix> "\""
OPERATOR   = <two-character operators> / <one-character operators>
```

## Base

A `program` is a series of statements.

```
program = statement* EOF
```

## Statement Types

A statement can be one of many. It can be a declaration, control-flow keywords, or an expression-statement. 

```
statement            = declaration / flow / expression-statement / block
expression-statement = expression ";"
```

We'll also define blocks here to help in the future.

```
block = "{" statement* "}"
```

### Declarations

Declarations can be declaring one of the following:
- Functions
- Variables
- Classes (TODO)

```
declaration = declaration-func
            / declaration-var

; fn NAME(PARAMS): RETURNS {...}
declaration-func = "fn" IDENTIFIER argument-declaration-list ":" IDENTIFIER func-inner
declaration-var  = IDENTIFIER IDENTIFIER ("=" expression) ";"

func-inner = "->" statement / block ; represents a shorthand function declaration or complete one
```

### Control Flow

Control flow keywords are one of the following:
- `if`
- `return`
- `while`

```
flow = flow-if / flow-return / flow-while

flow-if     = "if" "(" expression ")" statement ("else" statement)?
flow-return = "return" expression ";"
flow-while  = "while" "(" expression ")" statement
```

## Expressions

Now for the real stuff. Note: To prevent left-recursion, we define factors/sums/etc as a flat *list* of that type, or the thing below it.

```
expression = comma

comma = assignment ("," assignment)*

assignment-operators = "=" / "+=" / "-=" / "*=" / "%="
assignment = access assignment-operators assignment / logical-andor

logical-andor = equality ( ("&&" / "||") equality )*

equality = comparison ( ("==" / "!=") comparison )*

comparison = term ( (">=" / ">" / "<=" / "<") term )*

term = factor ( ("+" / "-") factor )*

factor = unary ( ("/" / "*") unary )*

unary = ( "!" / "-" / "++" / "--" ) unary / secondary

dot-access     = ("." IDENTIFIER)
bracket-access = ("[" expression "]")
access         = IDENTIFIER (dot-access / bracket-access)*

call = access argument-list*

secondary = call / access ("++" / "--")? / primary

group = "(" expression ")"

primary = primary-text / NUMBER / STRING / IDENTIFIER / group
```

## Helpers

To help us with shortening rules, we can also define these:

TODO: Default values for argument-declaration-list

```
argument-declaration-list = "(" (IDENTIFIER IDENTIFIER)? ("," IDENTIFIER IDENTIFIER)* ")"
argument-list             = "(" EXPRESSION? ("," EXPRESSION)* ")"
```

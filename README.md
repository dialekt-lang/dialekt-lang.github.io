# Dialekt

Dialekt is a very small language for representing boolean expressions of the
form often used by simple search engines.

Try the [online demo](http://dialekt.icecave.com.au)

## Rationale and Concepts

Dialekt was designed to filter a list of entities based on sets of strings,
typically human-readable "tags" or "labels". The developers had a need describe
such filters somewhat frequently, and being able to do so in a database and
search-engine agnostic manner has proven useful.

Dialekt implementations are provided in a range of different programming
languages. Each implementations provides:

- an **expression parser**, which parses Dialekt expressions into an [abstract syntax tree](http://en.wikipedia.org/wiki/Abstract_syntax_tree)
- a **list parser**, which parses space-separated tags into a set or similar data structure
- an **evaluator**, which checks if a given set of tags matches a specific Dialekt expression

Additionally, a mechanism allowing custom algorithms to traverse the syntax tree
is provided. This can be used, for example, to generate an SQL query that finds
entities with tags that match the expression.

## Implementations

- [PHP](https://github.cwx.io/IcecaveStudios/dialekt)
- [JavaScript](https://github.cwx.io/IcecaveStudios/dialekt-js)
- [Go](https://github.cwx.io/dialekt-lang/dialekt-go) (work in progress)

## Syntax

The example below is a Dialekt expression that uses the entire Dialekt grammar.
As you can see it is a simple language, reminiscent of SQL. You can see how this
expression is parsed using the [online demo](http://dialekt.icecave.com.au/?expr=foo+bar+wildcard*+AND+%28baz+OR+qux%29+AND+NOT+%22quoted+string%22).

    foo bar* AND (baz OR qux) AND NOT "string \"with\" quotes"

### Tags

`foo`, `bar`, `baz`, `qux` and `"string \"with\" quotes"` are *tags*. The tag
is the most basic syntactic element of an expression.

Tags are a sequence of any character except whitespace, parentheses and the
asterisk. Tags which include one or more asterisks form a *tag pattern*, which
behaves similarly to SQL's `LIKE` operator.

#### Quoted Tags

Tags may be enclosed in double-quotes, as the `"string \"with\" quotes"` tag has
been. Doing so allows tags to include parentheses and whitespace characters.

Tags *must* be enclosed in double-quotes if they are equal to one of the
reserved words `AND`, `OR` or `NOT`.

The backslash character (`\`) is used to escape the quote (`"`) and asterisk
(`*`) characters, allowing tags to include those characters directly. The
backslash character itself must also be escaped in this way.

### Logical Operators

An expression is composed of one or more tags, and the logical operators `AND`,
`OR` and `NOT`. The logical operators are case-insensitive.

Two adjacent tags have an implicit `AND` operator. Thus, the expression
`foo bar` is equivalent to `foo AND bar`.

Parentheses are used to specify the precedence of logical expressions. Where
parentheses are not used, the order of precedence is `NOT`, `AND`, then `OR`,
from highest to lowest. This, `foo OR bar AND baz` is equivalent to
`foo OR (bar AND baz)`.

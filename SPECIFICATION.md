# The Downson Specification

Version: 0.5.0

## Table of Contents

  * [Preliminaries](#preliminaries)
    * [Handling Parser Failures](#handling-parser-failures)
    * [Structure](#structure)
  * [Primer on Types](#primer-on-types)
    * [Primitive Literals](#primitive-literals)
    * [Built-in Primitive Types](#built-in-primitive-types)
    * [Custom Primitive Types](#custom-primitive-types)
    * [Complex Types](#complex-types)
  * [Object](#object)
    * [Syntax](#syntax)
    * [Notes](#notes)
  * [String](#string)
    * [Syntax](#syntax-1)
    * [Examples](#examples-1)
  * [Signed Integer](#signed-integer)
    * [Syntax](#syntax-2)
    * [Notes](#notes-1)
    * [Examples](#examples-2)
  * [Floating-Point Number](#floating-point-number)
    * [Syntax](#syntax-3)
    * [Examples](#examples-3)
  * [Boolean](#boolean)
    * [Examples](#examples-4)
  * [List](#list)
    * [Syntax](#syntax-4)
    * [Examples](#examples-5)

## Preliminaries

Downson (from markDOWN Object Notation) is an extension of the [Github Flavored Markdown](https://github.github.com/gfm/) syntax. Downson is an extension such that downson documents are valid GFM documents. Therefore implementors of this specification should first consult the GFM specification for guidelines.

### Handling Parser Failures

Parsers should implement the following behaviour in case of a parsing failure:

  * if a value (ie. an instance of a type, regardless of being a complex or primitive type) cannot be parsed, then
    * ignore the value,
    * ignore the corresponding object key
  * if an object key cannot be parsed, then
    * ignore the object key
    * ignore the value corresponding to the object key.

Even in case of a partial failure, the above guidelines should be followed. Some examples of partial failures:

  * the name and the binding direction of the object key can be parsed, but the alias is ill-formed,
  * the name of the object key can be parsed, but the binding direction is ill-formed.

Although in case of a partial failure, it would be possible to generate some data from the ill-formed section of the document, this would not reflect the original intention of the user. However, the client of the parser must be informed of the issues in form of a warning.

### Structure

A downson document is always an *object* which object is referred to as the *top-level object*. An empty downson document is an empty object.

## Primer on Types

In the following subsections, the types provided by downson are described.

### Primitive Literals

A literal representing a value of some primitive type (built-in or custom) is called a *primitive literal*. The syntax of *primitive literals* makes use of the [GFM Inline Link](https://github.github.com/gfm/#inline-link) syntax as follows:

  * *link text* is interpreted as the actual literal,
  * *link destination* contains the mandatory *type hint*,
  * the optional *link title* can contain a *value override*.

#### Link Text

The *link text* (characters between the square brackets) forms the actual literal. 

#### Link Destination - Type Hint

The *link destination* contains the *type hint* which describes the type of the *primitive literal*. The *type hint* can be the name of any built-in primitive type or the name of a custom primitive type.

#### Link Title - Value Override

A *value override* has the following properties:

  * overrides the literal value provided in the *link text*,
  * **must be** a valid value of the type defined by the *type hint*.

Overrides the literal value. Must be a valid literal of the type defined by the *type hint*.

#### Examples

Define the string `Hello, World!`:

~~~~
[Hello, World!](string)
~~~~

Define the integer value `42`:

~~~~
[42](int)
~~~~

*Value overrides* for boolenas:

~~~~
[vrai](bool "true")
[faux](bool "false")
~~~~

Float *value override*:

~~~~
[π](float "3.14")
~~~~

### Built-in Primitive Types

Downson has the following built-in primitive types:

  * string,
  * signed integer,
  * floating-point number,
  * boolean.

### Custom Primitive Types

On top of the built-in primitive types, custom primitive types can be added to downson.

An implementation of the downson specification is **required** to provide hooks for user-defined code to parse and process *primitive literals* of custom primitive types.

The syntax for custom type names are defined as follows:

  * the type name **must be** a valid *link destination*,
  * the type name **must** differ from the built-in type names:
      * `int`,
      * `string`,
      * `float`,
      * `boolean`,
      * `list`,
      * `object`.

### Complex Types

Complex types differ from primitive types in the following aspects:

  * apart from some edge-cases, values of complex types cannot be represented by literals,
  * as a consequence, *value override* is not defined for complex types.

Downson supports the following built-in complex types:

  * object,
  * list.

## Object

**Type hint**: `object` - only for the empty object literal

Objects are unordered key-value containers. Keys can be arbitrary strings while values can be of any type including the object type itself.

### Syntax

An empty object can be represented by the following literal syntax:

~~~~
[](object "empty")
~~~~

Note, that this counts as a special case *value override*, therefore the *link text* is ignored.

Object keys can be created using any of the following two forms.

#### Header Syntax

In what follows, we define the following terms and notations:

  * The object introduced by the previous heading is referred to as the *current object*. If there is no previous heading, then the *current object* is the *top-level* object.
  * The level of the previous heading is denoted as `p`.
  * The level of the new heading is denoted as `n`.

A [GFM ATX Heading](https://github.github.com/gfm/#atx-headings) creates a new empty object and registers it according to the following rules:

  * if `p = n`, then the new object is registered on the parent of the *current object*,
  * if `p > n`, then the new object is registered on the object created by a previous heading with depth `n - 1`,
  * if `n < p`, where `p - n = 1` then the new object is registered on the *current object*. Cases when `p - n > 1` are not permitted.

Subsequent keys defined with the *emphasis syntax* are registered on the *current object*, unless otherwise noted.

##### Key Alias

As described previously, by default the string that represents the new key is the heading text itself. To override this setting, one can use a *key alias*. A *key alias* has the following syntax:

~~~~
## Heading Text [](alias "key-alias")
~~~~

where the *link title* is the actual alias, and the string `alias` in the *link destination* is just a marker for parsing purposes.

The key is set to be the string between the dobule-quotes.

##### Ignore Alias

An *ignore alias* can be used to skip the contents until the next same- or upper-level heading entirely. The syntax of *ignore alias* is as follows:

~~~~
## Skip me [](ignore)
~~~~

#### Emphasis Syntax

Keys on the **current object** can be registered using the so-called *emphasis syntax*. The emphasis syntax consists of the following:

  * a mandatory `.` (dot) character,
  * a mandatory string describing the key name,
  * either an *ignore alias* or *key metadata*.

The syntax is as follows:

~~~~
**.key-name** ignore-alias or key-metadata
~~~~

##### Ignore alias

Ignore alias works the same way as in the case of the *heading syntax*:

~~~~
**.skip me** [](ignore)
~~~~

Note, that the *ignore alias* is only needed, when the key would be a syntactically valid downson object key.

##### Key metadata

The key metadata includes

  * the mandatory binding direction of the key, which can be `left` or `right`,
  * optionally whether the key introduces a new nested object or not,
  * an optional key alias.

The fields must be separated by `:` (colon) characters.

The exact syntax is as follows:

~~~~
[](left/right[:object][:alias] ["key-alias"])
~~~~

where the square brackets (except for the first pair) denote optional content.

###### Binding direction

The *binding direction* can take the following two values:

  * `left` if the value corresponding to the current key is on the **left** side of the key,
  * `right` if the value corresponding to the current key is on the **right** side of the key.

Binding to the right:

~~~~
The **.meaning of life** [](right) is [42](int).
~~~~

Binding to the left:

~~~~
My PC has [8](int) gigabytes of **.memory** [](left).
~~~~

###### Nesting and terminating

A key can be used to introduce a nested object using the `object` field in the *key metadata*. The `object` field tells the downson parser, that all keys between the current key and the next unmatched *object terminator* (or either the beginning/end of the file or a heading) in the binding direction should be registered on a new nested object.

The *object terminator* has the following syntax:

~~~~
[]($)
~~~~

A simple example with binding to the right:

~~~~Markdown
Here I describe the **.configuration** [](right:object) of my PC. It has [8](int) gigabytes of **.memory** [](left) and a [500](int) GB capacity **.hard drive** [](left:alias "hardDrive") []($).
~~~~

In this case, the object terminator ends the object which gets assigned to the `configuration` key. A JSON representation of the same data:

~~~~JSON
{
  "configuration": {
    "memory": 8,
    "hardDrive": 500
  }
}
~~~~

A more involved example is as follows:

~~~~Markdown
The **.server** [](right:object) should start with the following configuration. Talking about **.HTTP** [](right:object:alias "http") settings, it should listen on **.port** [](right) [8080](int) with a [100](int) ms **.timeout** [](left) []($). The **.base path** [](right:alias "basePath") should be set to [/server](string) []($). []($)The **.connection string** [](right:alias "connection") should be set to [i:dont:know](string) for the **.database** [](left:object).
~~~~

which has the following JSON representation:

~~~~JSON
{
  "server": {
    "http": {
      "port": 8080,
      "timeout": 100
    },
    "basePath": "/server"
  },
  "database": {
    "connection": "i:dont:know"
  }
}
~~~~

### Notes

The same key on the same object **must not** appear twice.

## String

**Type hint**: `string`

String is a primitive type consisting of a finite sequence of characters.

### Syntax

Simple string values can be represented using *primitive literals*. In addition to that, multiline strings can be represented in a verbatim (completely whitespace-preserving) form by piggybacking the [GFM Fenced Code Block](https://github.github.com/gfm/#fenced-code-blocks) syntax. However, *value overriding* is not possible for verbatim strings.

### Examples

~~~~
[Hello, World!](string)
[/home/downson/spec.md](string)
~~~~

~~~~
````
Hello,
  World

from a multiline string literal!
````
~~~~

## Signed Integer

**Type hint**: `int`

Represents a signed whole number. 

### Syntax

  * Can start with a leading positive or negative sign.
  * Leading zeros are not allowed.
  * Always decimal.
  * Digits can be grouped by any of the following characters: `_` (underscore), ` ` (single space), `.` (dot), `,` (comma). Different grouping characters can be mixed in the same literal, however that is considered to be bad practice. Grouping characters must be surrounded by at least one digit on both sides.

### Notes

Implementations are expected to provide at least 64 bits of storage space for signed integer values.

### Examples

~~~~
[100](int)
[-128](int)
[0100](int) <- INVALID!!!

[the meaning of life](int "42")

[+1 000 000](int) <- means 1000000
[1_000_000](int) <- means 1000000
[1.000.000](int) <- means 1000000

[1_123 0.0.0](int) <- BAD PRACTICE!!! Means 1123000.
~~~~

## Floating-Point Number

**Type hint**: `float`

The floating-point number type can used to represent real numbers. Implementations should handle downson's floating-point numbers as IEEE754 binary64 floating-point values.

### Syntax

An ordinary *primitive literal* of this type consists of three parts (strictly in this order):

  * a mandatory integer part,
  * an optional fractional part,
  * an optional exponent part.

The following *primitive literals* are also available:

  * positive infinity: `inf` or `+inf`,
  * negative infinity: `-inf`,
  * NaN: `nan`.

#### Integer Part

The integer part should be formed following the same rules that of the [Signed Integer](signed-integer) type with one subtle difference regarding grouping characters. If `.` (dot) is used as a grouping character, then the decimal separator character must be `,` (comma). Similarly, if `,` (comma) is used as a grouping character, then the decimal separator character must be `.` (dot).

#### Fractional Part

In the fractional part, grouping can be used in the same way as in the integer part.

#### Exponent Part

The exponent part must start with either `e` or `E`. Grouping is disallowed in the exponent part.

### Examples

~~~~
[infinity](float, "inf"),
[-0.0](float),
[100_00.12](float),
[5.55E-10](float)
~~~~

## Boolean

**Type hint**: `boolean`

Represents a truth value. Boolean has only two valid literal values:

  * `true`,
  * `false`.

### Examples

~~~~
[true](boolean)

[vrai](boolean "true") <- French for "true"
~~~~

## List

**Type hint**: `list` - only for the empty list literal

A list is an ordered container of heterogenous values. A list can contain values of any other type, including the list type itself.

### Syntax

An empty list can be represented by the following literal syntax:

~~~~
[](list "empty")
~~~~

Note, that this counts as a special case *value override*, therefore the *link text* is ignored.

#### GFM Ordered List Syntax

In simpler cases, mostly when storing primitive values or other lists in a list, the [GFM Ordered List](https://github.github.com/gfm/#ordered-list) syntax can be used. The contents of a GFM list element are inserted into the list.

#### GFM Table Syntax

When one would like to store objects with the same keys in a list, the [GFM Table](https://github.github.com/gfm/#tables-extension-) syntax can be used. In this case, the column names in the table header are going to be the keys of the stored objects, while the values in the table cells are going to be the values assigned to the respective keys.

  * *Key aliasing* can be used for column (and therefore, key) names.
  * *Ignore aliasing* can be used to ignore columns.
  * The same key may hold values of different types in different rows.

### Examples

Define a list of integers from 1 to 5:

~~~~
  1. [1](int)
  1. [2](int)
  1. [3](int)
  1. [4](int)
  1. [5](int)
~~~~

Define a list of two empty lists:

~~~~
  1. [](list "empty")
  1. [](list "empty")
~~~~

Define a list containing two numbers and a list of two floats (pay attention to the indentation!):

~~~~
  1. [73](int)
  1. [100](int)
  1.
      1. [8.32](float)
      1. [-9.331](float)
~~~~

Define a list of two objects using the table syntax:

~~~~Markdown
| Name [](alias "firstName") | Age [](alias "age")  | Comments [](ignore)         |
|----------------------------|----------------------|-----------------------------|
| [Alice](string)            | [23](int)            | Likes to send messages.     |
| [Bob](string)              | [34](int)            | Likes to received messages. |
~~~~

The JSON representation of the last example is as follows:

~~~~JSON
[
  {
    "firstName": "Alice",
    "age": 23
  },
  {
    "firstName": "Bob",
    "age": 34
  }
]
~~~~

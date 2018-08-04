# The Downson Specification

Version: 0.1.0

## Basics

Downson (from markDOWN Object Notation) is an extension of the [Github Flavored Markdown](https://github.github.com/gfm/) syntax. Downson is an extension such that downson documents are valid GFM documents. Therefore implementors of this specification should first examine the GFM specification for guidelines.

## Primer on Types

### Primitive Types

The following primitive types are supported:

  * string,
  * signed integer,
  * floating-point number,
  * boolean.

Primitive types **can be**

  * represented with literals,
  * value hinted.

#### Literal Value Syntax

Values of primitive types are represented by literals that in turn make use of the [GFM inline link](https://github.github.com/gfm/#inline-link) syntax. The *link text* is interpreted as the literal, while the *link destination* contains the mandatory *type hint* and the *link title* contains the optional *value override*.

In case of a parser failure, it is assumed, that the inline link element does not describe a downson literal value.

The following subsections describe the exact syntax.

##### Link Text

The link text (characters between the square brackets) forms the actual literal. 

##### Link Destination

The link text is divided into two sections:

###### Type hint

Defines the type of the literal. Can be the name of any primitive type or the name of a custom primitive type. The *type hint* is enclosed in curly braces.

###### Value override

Overrides the literal value. Must be a valid literal of the type defined by the *type hint*.

What's the point of value overrides?  As the primary goal of downson is to be human readable, there can be situations when the literal for the human reader and the actual data for the consuming application should be different. For example, in Hungarian, `igaz` means true and `hamis` means false. Therefore it is convenient to use value overrides in Hungarian documents:

~~~~
[igaz]({bool} "true")
[hamis]({bool} "false")
~~~~

Another example is the mathematical constant π:

~~~~
[π]({float} "3.14")
~~~~

##### Examples

Define the string `Hello, World!`:

~~~~
[Hello, World!]({string})
~~~~

Define the integer value `42`:

~~~~
[42]({int})
~~~~

For examples on value overrides, please see the section above.

#### Custom Primitive Types

On top of the predefined primitive types, custom primitive types can be added to Downson. Every compliant implementation of the downson specification is **required** to provide hooks for user-defined code to parse and process literals of custom types.

The syntax for custom type names are defined as follows:

  * the type name **must not** contain whitespace characters (as whitespace is defined by the [GFM specification](https://github.github.com/gfm/#whitespace)),
  * the type name **must** differ from the built-in type names:
      * `int`,
      * `string`,
      * `float`,
      * `boolean`,
      * `list`,
      * `object`.

<!---
#### Type Properties 
-->

### Complex Types

The following complex types are supported:

  * list,
  * object (or map).

Complex types **can**

  * contain values of other types.

Complex types (apart from some corner-cases) **cannot be**

  * represented with literals,
  * subject to a value override.

## Object

**Type hint**: `object` - only for the empty object literal

Objects are unordered key-value containers. Keys can be arbitrary strings while values can be of any type including the object type itself.

A literal or list is only processed by downson if it is assigned to some key of some object. By default, an empty downson document is an empty object. This empty object is referred to as the *top-level object*.

### Syntax

An empty object can be represented by the following literal syntax:

~~~~
[]({object} "empty")
~~~~

Note, that this counts as a *value override*, therefore the *link text* is ignored.

Keys can be created using any of the following two forms.

#### Header Syntax

Keys on the top-level object can be registered using level-one GFM ATX headings or with emphasis syntax keys that have no preceding heading. Nested objects can be created using headings of increased depth where the key is derived from the heading text "as is". We refer to the object introduced by the previous header as the *current object*, and denote the depth of the current heading with `c` and the depth of the new heading with `n`:

  * if `c = n`, then the new object is registered on the parent of the *current object*,
  * if `c > n`, then the new object is registered on the object created by a previous heading with depth `k - 1`,
  * if `n < c`, where `c - n = 1` then the new object is registered on the *current object*. Cases when `c - n > 1` are not permitted.

By default, new keys are registered on the heading's *current object*, unless otherwise noted.

##### Key alias and ignore alias

As described previously, the string that represents the new key is derived straight from the heading text. To override this setting, one can use a *key alias*. A key alias has the following syntax:

~~~~
## Heading Text [](alias "key-alias")
~~~~

where the link title (`key-alias`) is the actual alias, and the string `alias` (which is the link destination) must be present for parsing purposes.

The key is set to be the string between the parentheses.

One can also use an *ignore alias* to entirely skip the contents until the next same- or upper-level heading. The syntax of *ignore alias* is as follows:

~~~~
## Skip me []()

-- or --

## Skip me [](    ) <- whitespace-only
~~~~

Note, that between the heading text and the key/ignore alias, only whitespace can be placed.

#### Emphasis Syntax

Keys on the current object can be registered using the so-called *emphasis syntax*. The emphasis syntax consists of the following:

  * a mandatory `.` (dot) character,
  * a mandatory string describing the key name,
  * either an ignore alias or key-metadata

The syntax is as follows:

~~~~
**.key-name** ignore-alias or key-metadata
~~~~

##### Key Name

The actual name of the key must be preceded by a `.` (dot) character. The string after the `.` is the actual key name.

##### Ignore alias

Ignore alias works the same way as in the case of the *heading syntax*:

~~~~
**.skip me** []()
~~~~

Note, that the *ignore alias* is only needed, when a key is a syntactically valid downson key. In case of a parser error, no new key is created.

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

  * `left` if the value corresponding to the current key is on the **right** side of the key,
  * `right` if the value corresponding to the current key is on the **left** side of the key.

Binding to the right:

~~~~
The **.meaning of life** [](right) is [42]({int}).
~~~~

Binding to the left:

~~~~
My PC has [8]({int}) gigabytes of **.memory** [](left).
~~~~

###### Nesting and terminating

A key can be used to introduce a nested object using the `object` field in the key metadata. The `object` field tells the downson parser, that all keys between the current key and the next unmatched object terminator (or either the beginning/end of the file or a heading) in the binding direction should be registered on a new nested object.

The object terminator is defined as follows:

~~~~
[]($)
~~~~

A simple example with binding to the right:

~~~~
Here I describe the **.configuration** [](right:object) of my PC. It has [8]({int}) gigabytes of **.memory** [](left) and a [500]({int}) TB capacity **.hard drive** [](left:alias "hardDrive") []($).
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

~~~~
The **.server** [](right:object) should start with the following configuration. Talking about **.HTTP** [](right:object:alias "http") settings, it should listen on **.port** [](right) [80]({int}) with a [100]({int}) ms **.timeout** [](left) []($). The **.base path** [](right:alias "basePath") should be set to [/server]({string}) []($). []($)The **.connection string** [](right:alias "connection") should be set to [i:dont:know]({string}) for the **.database** [](left:object).
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

The same key **must not** appear twice.


## String

**Type hint**: `string`

String is a primitive type consisting of a finite sequence of characters.

### Syntax

Simple strings values can be represented using the normal literal syntax. In addition to that, multiline strings can be represented in a verbatim (completely whitespace-preserving) form by piggybacking the [GFM Code Block] syntax. However, *value overriding* is not possible for verbatim strings.

### Examples

~~~~
[Hello, World!]({string})
[/home/downson/spec.md]({string})
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
[100]({int})
[-128]({int})
[0100]({int}) <- INVALID!!!

[the meaning of life]({int} "42")

[1 000 000]({int}) <- means 1000000
[1_000_000]({int}) <- means 1000000
[1.000.000]({int}) <- means 1000000

[1_123 0.0.0]({int}) <- BAD PRACTICE!!! Means 1123000.
~~~~

## Floating-Point Number

**Type hint**: `float`

The floating-point number type can used to represent real numbers. Implementations should handle downson's floating-point numbers as IEEE754 binary64 floating-point values.

### Syntax

### Examples

## Boolean

**Type hint**: `boolean`

Represents a truth value. Boolean has only two valid literal values:

  * `true`,
  * `false`.

### Syntax

No special syntax.

### Examples

~~~~
[true]({boolean})

[vrai]({boolean} "true") <- French for "true"
~~~~

## List

**Type hint**: `list` - only for the empty list literal

A list is an ordered container of heterogenous values. A list can contain values of any other type, including the list type itself.

### Syntax

An empty list can be represented by the following literal syntax:

~~~~
[]({list} "empty")
~~~~

Note, that this counts as a *value override*, therefore the *link text* is ignored.

#### GFM Ordered List Syntax

In simpler cases, mostly when storing primitive values or other lists in a list, the [GFM ordered list](https://github.github.com/gfm/#ordered-list) syntax can be used. The contents of a GFM list element are inserted into the list.

#### GFM Table Syntax

When one would like to store object with the same keys in a list, the [GFM table] syntax can be used. In this case, the row names in the table header are going to be the keys of the stored objects, while the values in the table cells are going to be the values assigned to the respective keys.

  * Key aliasing can be used for row (and therefore, key) names.
  * The same key may hold values of different types in different objects.

### Examples

Define a list of number from 1 to 5:

~~~~
  1. [1]({int})
  1. [2]({int})
  1. [3]({int})
  1. [4]({int})
  1. [5]({int})
~~~~

Define a list of two empty lists:

~~~~
  1. []({list} "empty")
  1. []({list} "empty")
~~~~

Define a list containing two numbers and a list of two floats (keep attention to the indentation!):

~~~~
  1. [73]({int})
  1. [100]({int})
  1.
      1. [8.32]({float})
      1. [-9.331]({float})
~~~~

Define a list of two objects using the table syntax:

~~~~
| Name              | Age          |
|-------------------|--------------|
| [Alice]({string}) | [23]({int})  |
| [Bob]({string})   | [34]({int})  |
~~~~


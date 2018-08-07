# The Downson Specification

Version: 0.12.0

## Table of Contents

  * [Preliminaries](#preliminaries)
    * [Filename Extension](#filename-extension)
    * [Structure](#structure)
    * [Significant, Well-formed and Ill-formed Elements](#significant-well-formed-and-ill-formed-elements)
    * [Failure Handling](#failure-handling)
  * [Primer on Types](#primer-on-types)
    * [Primitive Literals](#primitive-literals)
    * [Built-in Primitive Types](#built-in-primitive-types)
    * [Custom Primitive Types](#custom-primitive-types)
    * [Complex Types](#complex-types)
  * [Object](#object)
    * [Syntax](#syntax)
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

In the subsequent sections, *Annotation* blocks are present to highlight the design decisions and considerations when creating this specification.

### Filename Extension

Downson has no special extension. The usual Markdown filename extension should be used.

### Structure

A downson document is always an *object* which object is referred to as the *top-level object*. An empty downson document is an empty object.

A document can be viewed from two perspectives:

  * elements that influence the appearance of the output generated from the downson document form the *presentation layer*,
  * elements that influence the shape and contents of the data generated from the downson document form the *data layer*.

### Significant, Well-formed and Ill-formed Elements

A GFM element is considered to be *significant* if it might influence either the *presentation* or the *data layer* of the downson document. 

The *significant elements* are the following:

  * [ATX headings](https://github.github.com/gfm/#atx-headings) and [Setext headings](https://github.github.com/gfm/#setext-headings),
  * [Indented code blocks](https://github.github.com/gfm/#indented-code-blocks) and [Fenced code blocks](https://github.github.com/gfm/#fenced-code-blocks),
  * [Paragraphs](https://github.github.com/gfm/#paragraphs),
  * [Tables](https://github.github.com/gfm/#tables-extension-),
  * [Ordered lists](https://github.github.com/gfm/#ordered-list),
  * [Unordered lists](https://github.github.com/gfm/#lists) - however, they carry no special meaning: an unordered list should be parsed by simply parsing the contents inside its items,
  * [Links](https://github.github.com/gfm/#links),
  * [Strong emphasis](https://github.github.com/gfm/#emphasis-and-strong-emphasis) (**NOT** emphasis).

Other elements are **ignored**.

A *well-formed element* is a *significant element*, that can be successfully interpreted as either an *object key* or a value of some type. *Significant elements* which are not *well-formed* are said to be *ill-formed*. *Ill-formed* elements are **ignored**.

### Failure Handling

Implementations should resort themselves to a highly flexible failure handling approach. This is a consequence of the fact, that downson is the extension of an existing format. To embrace a user-friendly behaviour, implementations should always produce at least an empty object as the result of the parsing process. On the other hand, all issues should be reported to the clients, sorted into two categories:

  * ambiguous syntax, ie. when the intention in the document might have been unclear,
  * downson interpretation errors.

If at the end of the processing, only issues caused by ambiguous syntax are present, then the *data layer* might still be completely valid. Respectively, the presence of interpretation errors is almost always a sign of a *data layer* corruption.

The exact failure handling behaviour is defined in *Failure* blocks.

## Primer on Types

In the following subsections, the types provided by downson are described.

### Primitive Literals

A literal representing a value of some primitive type (built-in or custom) is called a *primitive literal*. The syntax of *primitive literals* makes use of the [GFM Inline Link](https://github.github.com/gfm/#inline-link) syntax as follows:

  * *link text* is interpreted as the actual literal,
  * *link destination* contains the mandatory *type hint*,
  * the optional *link title* can contain a *value override*.

<details>
<summary>Annotation</summary>

The GFM Inline Link syntax was a good fit for literals. Regarding the *presentation layer*, inline links are always highlighted, which makes them stand out from their surrounding context that is a desirable feature when defining data. In addition to that, the *link destination* and *link title* parts make it possible to store metadata influencing the *data layer* without polluting the *presentation layer*.

</details>

<details>
<summary>Failure</summary>

When ignoring an ill-formed *primitive literal*, a

</details>

#### Link Text

The *link text* forms the actual literal.

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If the *link text* is empty (ie. whitespace-only), then the *primitive literal* is *ill-formed*.

**Interpretation Error**

  * If the *type hint* describes a known type but neither the *link text*, nor the *value override* describe a valid literal of that type, then the *primitive literal* is *ill-formed* and both the *primitive literal* itself and the corresponding *object key* should be **ignored**.

</details>

#### Link Destination - Type Hint

The *link destination* contains the *type hint* which describes the type of the *primitive literal*. The *type hint* can be the name of any built-in primitive type or the name of a custom primitive type.

<details>
<summary>Failure</summary>

**Ambigouous Syntax**

  * If the *type hint* describes an unknown type, then the *primitive literal* is *ill-formed*.

</details>

#### Link Title - Value Override

A *value override* has the following properties:

  * overrides the literal value provided in the *link text*,
  * **must be** a valid value of the type defined by the *type hint*.

<details>
<summary>Annotation</summary>

The *value override* feature was inspired by the two-faced (*presentation* and *data layer*) nature of downson documents. Situations can arise, when using an alias (override) for the same literal would increase the readability of the *presentation layer* but at the same time, the *data layer* should not be altered. Booleans make for a good example. Consider a downson document written in Hungarian. Hindering the document with Booleans literals like `true` or `false` would greatly perturb the *presentation layer*. Thanks to *value overrides*, one is not forced to use the well-formed *primitive literals* in the *presentation layer*.

The *link title* feature is a perfect fit for *value overrides*. It is optional, just as *value overrides* are. By default, it does not modify directly the *presentation layer*, but is only visible when the user hovers the mouse over an inline link.

One should be aware, however, not to abuse the *value override* syntax, because overusing or misusing it can create a huge gap between the *presentation layer* and *data layer*.

</details>

#### Examples

Define the string `Hello, World!`:

~~~~
[Hello, World!](string)
~~~~

Define the integer value `42`:

~~~~
[42](int)
~~~~

*Value overrides* for booleans:

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

<details>
<summary>Annotation</summary>

When deciding, which primitive types to support out of the box, we wanted to take a conservative approach. Instead of trying to "get right" types for dates, currencies and such, we decided not to include them in the specification at all. The reasoning behing this decision is the following.

Having a small set of built-in types makes the specification more concise and easier to implement. Ease of implementation is very important in the case of an emerging standard. Moreover, there are several programming languages that does not have built-in support for dates or the constant `null`. It seemed like a sensible choice, to miss out types like these, if client environments do the same.

However, we are aware, that a modern standard should provide a way to represent the types we have intentionally left out. That's why downson has the *custom primitive types* feature. Although, this might lead to the fragmentation of the downson ecosystem, we believe that for example community-driven extensions for other types can be a better solution than including them right into the standard and therefore requiring every implementation to support them.

</details>

### Custom Primitive Types

On top of the built-in primitive types, custom primitive types can be added to downson.

An implementation of the downson specification is **required** to provide hooks for user-defined code to parse and process *primitive literals* of custom primitive types.

The syntax for custom primitive type names are defined as follows:

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
[empty object](object "empty")
~~~~

Note, that this counts as a special *value override*.

Object keys can be created using any of the following two forms.

#### Header Syntax

In what follows, we define the following terms and notations:

  * The object introduced by the previous heading is referred to as the *current object*. If there is no previous heading, then the *current object* is the *top-level* object.
  * The level of the previous heading is denoted as `p`.
  * The level of the new heading is denoted as `n`.

A [GFM ATX Heading](https://github.github.com/gfm/#atx-headings) or a [GFM Setext Heading](https://github.github.com/gfm/#setext-headings) creates a new empty object and registers it according to the following rules:

  * if `p = n`, then the new object is registered on the parent of the *current object*,
  * if `p > n`, then the new object is registered on the object created by a previous heading with depth `n - 1`,
  * if `n < p`, where `p - n = 1` then the new object is registered on the *current object*. Cases when `p - n > 1` are not permitted.

Only the following two forms are considered to be valid instances of the *header syntax*:

  * a heading containing simple text only (ie. no emphasis or other inline elements). In this case the text is trimmed from both the left and the right and is taken as the key of the newly created object.
  * a heading containing simple text followed by a single key alias or a single ignore alias only.

Subsequent keys defined with the *emphasis syntax* are registered on the *current object*, unless otherwise noted.

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If the `n < p` where `p - n = 1` nesting rule is violated, then all subsequent headers and content should be **ignored** until detecting a header where `p = n` or `p > n`.
  * If the heading is of invalid syntax, then all subsequent headers and content should be **ignored** until detecting a header where `p = n` or `p > n`.

</details>

##### Key Alias

By default the string that represents the new key is the heading text itself. To override this setting, one can use a *key alias*. A *key alias* has the following syntax:

~~~~
## Heading Text [](alias "key-alias")
~~~~

where the *link title* is the actual alias, and the string `alias` in the *link destination* is just a marker for parsing purposes.

The key is set to be the string between the dobule-quotes.

<details>
<summary>Annotation</summary>

*Key alias* makes use of an invisible inline link to represent the alias. One could argue, that we should use the

~~~~
## [Heading Text](alias "key-alias")
~~~~

syntax instead. This, however, would greatly influence the *presentation layer* and, at the same time, would be too similar to the *primitive literal* syntax. These are the reasons, that made us to go with the invisible link syntax.

</details>

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If the *link text* is not empty, then all subsequent headers and content should be **ignored** until detecting a header where `p = n` or `p > n`.

**Interpretation Error**

  * If the `alias` marker is **not** followed by some valid *link text*, then all subsequent headers and content should be **ignored** until detecting a header where `p = n` or `p > n`.

</details>

##### Ignore Alias

An *ignore alias* can be used to skip the contents until the next same- or upper-level heading entirely. The syntax of *ignore alias* is as follows:

~~~~
Skip me [](ignore)
==================
~~~~

<details>
<summary>Failure</summary>

**Ambiguous Sytnax**

  * If the `ignore` marker is followed by some valid *link text*, then all subsequent headers and content should be **ignored** until detecting a header where `p = n` or `p > n`.

</details>

#### Emphasis Syntax

Keys on the **current object** can be registered using the so-called *emphasis syntax* which is based on the [GFM Emphasis](https://github.github.com/gfm/#emphasis-and-strong-emphasis) syntax. The emphasis syntax consists of the following:

  * a `.` (dot) character and the string describing the key name enclosed in a GFM Emphasis element,
  * *key metadata* inside a GFM Inline Link element.

~~~~
**.key-name** key-metadata
~~~~

Emphasis syntax does not require the notion of *ignore alias*. If a syntactically valid (ie. starting with a dot) emphasis is read without an accompanying *key metadata*, then it is automatically ignored.

Between the GFM Emphasis containing the key name and GFM Inline Link containing the key-metadata, only spaces or tabs are allowed.

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If the text inside the emphasis start with a dot, but the emphasis is not followed by *key metadata*, then the *object key* is *ill-formed*.

</details>

##### Key metadata

The key metadata includes (strictly in this order)

  * the mandatory binding direction of the key, which can be either `left` or `right`,
  * optionally whether the key introduces a new nested object or not,
  * an optional key alias.

The fields must be separated by `:` (colon) characters.

The exact syntax is as follows:

~~~~
[](left/right[:object] ["key-alias"])
~~~~

where the square brackets (except for the first pair) denote optional content.

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If the *link destination* is not any of the following:
      * `left`,
      * `right`,
      * `left:object`,
      * `right:object`,
    
    then the *object key* is *ill-formed*.
  * If the *link text* is not empty, then the *object key* is *ill-formed*.

</details>

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

<details>
<summary>Annotation</summary>

*Binding directions* is arguably the most complex feature of downson. We could go with binding to the right only, as it covers most of the cases. However, we did not want downson to get in the way and restrain the grammar constructs that can be used. Thus, binding to the left is possible.

</details>

###### Nesting and terminating

A key can be used to introduce a nested object using the `object` field in the *key metadata*. The `object` field tells the downson parser, that all keys between the current key and the next unmatched *object terminator* in the binding direction should be registered on a new nested object.

The *object terminator* has the following syntax:

~~~~
[]($)
~~~~

A simple example with binding to the right:

~~~~Markdown
Here I describe the **.configuration** [](right:object) of my PC. It has [8](int) gigabytes of **.memory** [](left) and a [500](int) GB capacity **.hard drive** [](left "hardDrive") []($).
~~~~

In this case, the *object terminator* ends the object which gets assigned to the `configuration` key. A JSON representation of the same data:

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
The **.server** [](right:object) should start with the following configuration. Talking about **.HTTP** [](right:object "http") settings, it should listen on **.port** [](right) [8080](int) with a [100](int) ms **.timeout** [](left) []($). The **.base path** [](right "basePath") should be set to [/server](string) []($). []($)The **.connection string** [](right "connection") should be set to [i:dont:know](string) for the **.database** [](left:object).
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

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If a key is already registered on an object, then the current *object key* is *ill-formed*.

**Interpretation Error**

  * At the end of the document, all unterminated nested objects should be closed, and registered with the appropriate keys.
  * At the next header, all unterminated nested objects should be closed, and registered with the appropriate keys.
  * If a *left-binding* *object key* is detected witout a matching *object terminator*, then the *object key* is *ill-formed*. However, already registered keys and values should remain intact.

</details>

## String

**Type hint**: `string`

String is a primitive type consisting of a finite sequence of characters.

### Syntax

Simple string values can be represented using *primitive literals*. In addition to that, multiline strings can be represented in a verbatim (completely whitespace-preserving) form by piggybacking the [GFM Fenced Code Block](https://github.github.com/gfm/#fenced-code-blocks) and [GFM Indented Code Block](https://github.github.com/gfm/#indented-code-blocks) syntax. However, *value overriding* is not possible for verbatim strings.

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
  * Digits can be grouped by any of the following characters: `_` (underscore), ` ` (single space), `.` (dot), `,` (comma). Different grouping characters can be mixed in the same literal. Grouping characters must be surrounded by at least one digit on both sides.

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
[1 000.000](int) <- means 1000000
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

Note, that this counts as a special *value override*, therefore the *link text* is ignored.

#### GFM Ordered List Syntax

In simpler cases, mostly when storing primitive values or other lists in a list, the [GFM Ordered List](https://github.github.com/gfm/#ordered-list) syntax can be used. The contents of a GFM list element are inserted into the list.

#### GFM Table Syntax

When one would like to store objects with the same keys in a list, the [GFM Table](https://github.github.com/gfm/#tables-extension-) syntax can be used. In this case, the column names in the table header are going to be the keys of the stored objects, while the values in the table cells are going to be the values assigned to the respective keys.

  * *Key aliasing* can be used for column (and therefore, key) names.
  * *Ignore aliasing* can be used to ignore columns.
  * The same key may hold values of different types in different rows.

There are two valid syntactical forms for heading cells:
  * a cell containing simple text only (ie. no emphasis or other inline elements). In this case the text is trimmed from both the left and the right and is taken as the key.
  * a cell containing simple text followed by a single key alias or a single ignore alias only.

Normal (ie. non-heading) cells can only contain a single *primitive literal*

<details>
<summary>Failure</summary>

**Ambiguous Syntax**

  * If a heading cell is syntactically invalid or its contents are ill-formed, then the **whole** table is **ignored**.
  * If a normal (ie. non-heading) cell is syntactically invalid or its contents are ill-formed, then the **whole** table is **ignored**.

</details>

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
| [Bob](string)              | [34](int)            | Likes to receive messages.  |
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

# Tests [](alias "tests")

Downson has an official test suite to ensure that different implementations share a common behaviour.

This file contains the listing of the test cases. For each case, this file defines the following:

  * the name of the test case (`name` property),
  * the filename of the test case (`filename`),
  * a description (ignored),
  * the tested features (ignored),
  * whether the test case has *interpretation errors* or not˙(`hasInterpretationErrors`).

The [tests](tests) directory contains the actual test cases. Each test case has two files:

  * the downson file to be parsed (`test-case-filename.md`),
  * a JSON file that contains the same data as the data layer of the downson file (`test-case-filename.json`).

The actual test **.cases** [](right) are the following:

  1. **..** [](right:object)
      * **.Name** [](right "name") - [Empty](string)
      * **.Filename** [](right "filename") - [empty](string)
      * **Description** - A completely empty markdown file.
      * **Features**
        * Empty top-level object.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Nesting with Heading](string)
      * **.Filename** [](right "filename") - [nesting-with-heading](string)
      * **Description** - A markdown file with several headings creating nested objects.
      * **Features**
        * Object Keys - Heading syntax.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Nesting with Emphasis](string)
      * **.Filename** [](right "filename") - [nesting-with-emphasis](string)
      * **Description** - A markdown file with several nested objects, created using the emphasis syntax.
      * **Features**
        * Object keys - Emphasis syntax.
        * Nested objects with `object` key metadata.
        * Left and right binding.
        * Object terminators.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Nesting with Emphasis and Unordered Lists](string)
      * **.Filename** [](right "filename") - [nesting-with-emphasis-and-unordered-lists](string)
      * **Description** - A markdown file with several nested objects, created using the emphasis syntax, embedded into unordered lists.
      * **Features**
        * Object keys - Emphasis syntax.
        * Nested objects with `object` key metadata.
        * Left and right binding.
        * Object terminators.
        * Unordered lists.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Nesting Lists](string)
      * **.Filename** [](right "filename") - [nesting-lists](string)
      * **Description** - Multiple levels of nested lists.
      * **Features**
        * List - Ordered List syntax.
        * Right binding.
        * Empty list literal.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Lists with Table Syntax](string)
      * **.Filename** [](right "filename") - [lists-with-table-syntax](string)
      * **Description** - Lists of objects created with the Table Syntax.
      * **Features**
        * List - Table syntax.
        * Primitive literals (*string*, *int*, *float*).
        * Right binding.
        * Key alias.
        * Ignore alias.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Integer Literals](string)
      * **.Filename** [](right "filename") - [integer-literals](string)
      * **Description** - A list of integer literals.
      * **Features**
        * Parsing *int* primitive literals.
        * List - Ordered List syntax.
        * Object keys - Emphasis syntax.
        * Right binding.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [String Literals](string)
      * **.Filename** [](right "filename") - [string-literals](string)
      * **Description** - A list of integer literals.
      * **Features**
        * Parsing *string* primitive literals.
        * List - Ordered List syntax.
        * Object keys - Emphasis syntax.
        * Right binding.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Boolean Literals](string)
      * **.Filename** [](right "filename") - [boolean-literals](string)
      * **Description** - A list of boolean literals.
      * **Features**
        * Parsing *boolean* primitive literals.
        * List - Ordered List syntax.
        * Object keys - Emphasis syntax.
        * Right binding.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Float Literals](string)
      * **.Filename** [](right "filename") - [float-literals](string)
      * **Description** - A list of float literals.
      * **Features**
        * Parsing *float* primitive literals.
        * List - Ordered List syntax.
        * Object keys - Emphasis syntax.
        * Right binding.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [Value Overrides](string)
      * **.Filename** [](right "filename") - [value-overrides](string)
      * **Description** - Value overrides for primitive types.
      * **Features**
        * Parsing various primitive literals.
        * Value overrides.
        * Object keys - Emphasis syntax.
        * Right binding.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)
  1. **..** [](right:object)
      * **.Name** [](right "name") - [ECC](string)
      * **.Filename** [](right "filename") - [ecc](string)
      * **Description** - The ECC example.
      * **.Has interpretation errors?** [](right "hasInterpretationErrors") - [no](boolean "false") []($)

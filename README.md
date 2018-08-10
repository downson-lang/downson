# Downson

markDOWN Object Notation

Downson makes it possible to embed typed and structured data into Markdown documents.

**Specification**

Read the specification here: [SPECIFICATION.md](SPECIFICATION.md).

**Examples**

Examples can be found in the [examples](examples) directory.

**Tests**

This repository contains a test suite that can be used to exercise downson implementations. The description of the test cases can be found in the [TESTS.md](TESTS.md) downson file.

## Motivation

Downson was created out of pure frustration. Applications ship with tons of configuration and data files that contain crucial settings and information. Yet, correctly documenting these is a horrible burden. One can write the documentation in a separate file, exposing themselves to an increased risk of diverging settings and documentation, or  write some sort of rudimentary documentation using in-place comments (assuming, the spec has them).

Current formats embed documentation into the data.

Downson inverts this principle. Data is embedded right into the documentation, offering a structured and sane approach for both the data and its documentation.

Summing this up:

![Roll safe your configuration!](img/roll-safe.jpg)

## Goals and non-goals

  * Downson aims to be easily read by **humans** and easily written by **humans**.
  * Downson is **NOT** a data exchange format. It is best suited for configuration and other read-only data files.

## Implementations

No implementations yet :frowning:.

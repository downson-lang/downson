# Benchmarks [](alias "benchmarks")

In this document, the benchmarking configuration of the SuperDuper [ECC](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) library is described.

The library has separate C and Java implementations, thus they will have separate configurations as well. 

## Java [](alias "java")

For Java, the most important thing to consider is the warmup iterations. Optimizations performed by the JVM have a great impact on the performance of the code.

### Scenarios [](alias "scenarios")

The benchmarks we would like to perform are divided into so-called scenarios.

#### Affine Point Multiplication [](alias "affineMultiplication")

Affine point multiplication is a crucial operation in our library, and has critical performance **.requirements** [](right):

| Length (in bits) [](alias "length")  | Max time (in millisecods) [](alias "max") | Avg time (in milliseconds) [](alias "avg") |
|--------------------------------------|-------------------------------------------|--------------------------------------------|
| [128](int)                           | [3](int)                                  | [2](int)                                   |
| [512](int)                           | [5](int)                                  | [3](int)                                   |
| [1024](int)                          | [10](int)                                 | [5](int)                                   |

**.Test cases** [](right "cases") are therefore the following:

| Length (in bits) [](alias "length")  | Warmup iterations [](alias "warmup") | Test iterations [](alias "test") | Comments [](ignore) |
|--------------------------------------|--------------------------------------|----------------------------------|---------------------|
| [128](int)                           | [5](int)                             | [20](int)                        | None.               |
| [512](int)                           | [5](int)                             | [20](int)                        | None.               |
| [1024](int)                          | [10](int)                            | [20](int)                        | Bit more warmup needed, because of JVM memory handling. |

#### Jacobian Point Doubling [](alias "jacobianDouble")

Jacobian point doubling is mostly used as a special case of point addition (adding a point to itself). It's another hot code, thus we need to ensure, it's fast. The **.requirements** [](right) are the following:

| Length (in bits) [](alias "length")  | Max time (in millisecods) [](alias "max") | Avg time (in milliseconds) [](alias "avg") |
|--------------------------------------|-------------------------------------------|--------------------------------------------|
| [128](int)                           | [1](int)                                  | [0.55](float)                              |
| [512](int)                           | [2](int)                                  | [1.25](float)                              |
| [1024](int)                          | [3](int)                                  | [1.75](float)                              |

And the **.test cases** [](right "cases"):

| Length (in bits) [](alias "length")  | Warmup iterations [](alias "warmup") | Test iterations [](alias "test") |
|--------------------------------------|--------------------------------------|----------------------------------|
| [128](int)                           | [5](int)                             | [20](int)                        |
| [512](int)                           | [5](int)                             | [20](int)                        |
| [1024](int)                          | [5](int)                             | [20](int)                        |

#### Encrypt-Decrypt [](alias "encryptDecrypt")

We've implemented encryption and decryption over Type-1 elliptic curves. In this case, we do not state requirements and cases, but some **.common settings** [](right:object "common") for all cases:

  * there should be [10](int) **.warmup** [](left) and [20](int) **.test** [](left) iterations for all cases,
  * the following **.metrics** [](right) should be recorded:
      1. [average time](string "AverageTime")
      1. [maximum and minimim time](string "SampleTime")
  * the **.file** [](right) containing the test data can be found at [./config/benchmarks/java/encrypt-decrypt](string).[]($)

## C [](alias "c")

Regarding C, the above requirements are somewhat altered, because
  
  * we assume, that the code is going to be faster than the Java implementation,
  * no warmup is needed.

### Scenarios [](alias "scenarios")

#### Affine Point Multiplication [](alias "affineMultiplication")

The hardened performance **.requirements** [](right) are the following:

| Length (in bits) [](alias "length")  | Max time (in millisecods) [](alias "max") | Avg time (in milliseconds) [](alias "avg") |
|--------------------------------------|-------------------------------------------|--------------------------------------------|
| [128](int)                           | [1](int)                                  | [0.5](float)                               |
| [512](int)                           | [2](int)                                  | [1.1](float)                               |
| [1024](int)                          | [3](int)                                  | [1.66](float)                              |

Corresponding **.test cases** [](right "cases"):

| Length (in bits) [](alias "length")  | Test iterations [](alias "test")     |
|--------------------------------------|--------------------------------------|
| [128](int)                           | [10](int)                            |
| [512](int)                           | [10](int)                            |
| [1024](int)                          | [15](int)                            |

#### Jacobian Point Doubling [](alias "jacobianDouble")

The **.requirements** [](right) are the following:

| Length (in bits) [](alias "length")  | Max time (in millisecods) [](alias "max") | Avg time (in milliseconds) [](alias "avg") |
|--------------------------------------|-------------------------------------------|--------------------------------------------|
| [128](int)                           | [0.45](float)                             | [0.23](float)                              |
| [512](int)                           | [0.85](float)                             | [0.43](float)                              |
| [1024](int)                          | [1.25](float)                             | [0.73](float)                              |

And the **.test cases** [](right "cases"):

| Length (in bits) [](alias "length")  | Test iterations [](alias "test")     |
|--------------------------------------|--------------------------------------|
| [128](int)                           | [10](int)                            |
| [512](int)                           | [10](int)                            |
| [1024](int)                          | [15](int)                            |

#### Encrypt-Decrypt [](alias "encryptDecrypt")

Again, we will only define some **.common settings** [](right:object "common") for all cases:

  * there should be [30](int) **.test** [](left) iterations for all cases,
  * the **.file** [](right) containing the test data can be found at [./config/benchmarks/c/encrypt-decrypt](string).[]($)

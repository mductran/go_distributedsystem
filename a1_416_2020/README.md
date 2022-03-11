  Assignment 1

[416](https://www.cs.ubc.ca/~bestchai/teaching/cs416_2020w2/index.html) Distributed Systems: Assignment 1 \[BigUInt\]
---------------------------------------------------------------------------------------------------------------------

### Due: January 17 at 6pm PST

Winter 2020

In this assignment you will get started with programming in the Go language. To solve this assignment you will need to install Go, figure out how to compile, run, and debug a Go program, and implement a few key pieces in a partial BigUInt codebase that we will give to you.

#### Overview

Go (or `golang`, as it helps to search for it) is an imperative programming language generally aimed at the development of distributed systems. In some ways, it is related to systems languages like C and Rust, in that programs are built using structs and functions (and like Rust, while it supports "methods", they are basically just special functions). In other ways it is more similar to managed languages like C# and Java, in that, while it does not have a VM, it has a lot of "managed" features: it has a garbage collector, full runtime type information, and the design strives to have essentially no undefined behaviour.

This assignment's objective in particular is to help you get to grips with Go's basic features. You will learn:

*   Some basic data structure usage and management [(slices, primarily)](https://blog.golang.org/slices-intro)
*   How to work with simple [struct](https://gobyexample.com/structs) definitions and methods
*   How to work with Go's built-in [testing infrastructure](https://golang.org/pkg/testing/)

A BigUInt is a simplified version of a BigInt, just like [the one that ships in Go's standard library](https://golang.org/pkg/math/big/), or the default int implementation in Python, or the equivalent data structure available in most programming languages. At a high level, the goal of a Big(U)Int is to store unbounded-precision integers. In many programming languages, like C, C++, Java, and Go, the built-in integer types can only hold number represented by their _bit width_, which will often be 32 or 64. While this may be good enough for many situations, sometimes a truly unbounded number is needed, or, in Python's case, an unbounded number is provided by default for usability's sake.

This is the BigInt: it represents a number a variable-length sequence of machine ints, requiring any int-supported operations to be implemented as array manipulations. BigUInt is, like the uint type in Go or unsigned int in C, an unsigned variant of BigInt that does not need to support negative numbers, making implementation a little simpler.

#### BigUInt

This assignment's specific variant of BigUInt is already pre-defined for you by us: it is a struct containing a reference to a slice of unsigned bytes. This means that your task is to implement a pair of unsigned integer operations on slices of unsigned bytes: addition, subtraction, and deep copying.

In addition to Go's [extensive documentation](https://golang.org/doc/), of which we recommend [the tour](https://tour.golang.org/) for language features, and [How to Write Go Code](https://golang.org/doc/code.html) for the package and testing concepts, the provided assignment code aims to be an example of how to lay out a Go project.

We provide you with three files: (1) [`biguint.go`](https://www.cs.ubc.ca/~bestchai/teaching/cs416_2020w2/assign1/biguint.go), (2) [`biguint_test.go`](https://www.cs.ubc.ca/~bestchai/teaching/cs416_2020w2/assign1/biguint_test.go), (3) [`go.mod`](https://www.cs.ubc.ca/~bestchai/teaching/cs416_2020w2/assign1/go.mod).

The file `biguint.go` has some code that helps to figure things out. This file includes the `bytesFromUInt64` method, which unpacks a 64-bit unsigned integer into a slice of bytes, and `(*BigUInt).String()`, which is a standard stringification method for BigUInt instances.

We also provide some testing infrastructure in `biguint_test.go` to help guide you in testing this first assignment, and to give you a base to work from when building your own tests in subsequent assignments.

The `go.mod` file is a _module definition_, whose full implications are discussed in [Using Go Modules](https://blog.golang.org/using-go-modules). Generally speaking, the file defines the root directory of a collection of Go packages, a canonical name by which one can import those packages, and any dependency information. In this case, the canonical name is `example.org/cpsc416/a2`, rooted at [example.org](https://example.org/) due to this being a course assignment. There is no additional dependency information, except `go 1.14`, which indicates the version of the Go toolchain that initialized the module, and defines the provided code's Go language compatibility.

You should only modify the code in `biguint.go`. As a starting point, make sure you can run the provided tests, which should fail.

#### BigUInt API

  
**The following API calls below are defined and we also provide their implementations:**

*   type BigUInt struct {
      data \[\]uint8
    }
    
    Our BigUInt type definition, containing a slice of unsigned bytes. Unsigned ints should be split up into 2 digit base 16 chunks, indexed from least to most significant, e.g., `[]uint8{ 0x00, 0xFF }` represents `0xFF00`. This is also an example of a [slice type](https://blog.golang.org/slices-intro).
    
*   var ErrUnderflow = errors.New("arithmetic underflow")
    
    This is the underflow error for subtraction. Go's error handling is minimalistic by design, and can be confusing when coming from languages that have more heavyweight error handling support. Some [general guidance](https://blog.golang.org/error-handling-and-go) is available on the basic idioms of Go error handling. Additionally, Go 1.13 and above adds [the ability to standard-ly nest one error inside another](https://blog.golang.org/go1.13-errors), which may be useful in larger projects, where one kind of error might cause another.
    
    In this assignment, you just have to return this error in the correct situation.
    
*   func NewBigUInt(i uint64) \*BigUInt
    
    The constructor for a BigUInt, based on a uint64. This function relies on `bytesFromUInt64`, which takes an unsigned 64-bit integer and converts it into an array of bytes, following the scheme for this assignment (least to most significant bytes). Note that the resulting slice and therefore the underlying bytes of the `BigUInt` do not include any leading zeroes, stopping at the most significant non-zero byte.
    
*   func (x \*BigUInt) Bytes() \[\]uint8
    
    Provides access to the raw bytes underlying a given BigUInt.
    
*   func (x \*BigUInt) String() string
    
    This function demonstrates some more slice-based code. It generates a string representing `x`, under the following scheme:
    
    *   digits should be printed in base 16, with lowercase letters
    *   groups of 8 digits should be separated by underscores
    *   no leading zeroes should be printed
    *   the string should be prefixed with "0x"
    
    See the golang's printf-style string formatting [manual](https://golang.org/pkg/fmt/#Formatter).
    

  
**The three API calls below are defined for you, but you have to provide the implementation:**

*   func (x \*BigUInt) Copy() \*BigUInt
    
    Generates an exact and fully independent (deep) copy of a given BigUInt. Note that the copy should not share any memory with `x`.
    
*   func (x \*BigUInt) Add(y \*BigUInt) \*BigUInt
    
    The Add method increases `x` by the number represented by `y`, and should return a pointer to `x`. Note that `x`'s slice's size may increase as a result of this operation. More specifically, `x`'s slice should have the minimum length necessary to represent the resulting sum.
    
*   func (x \*BigUInt) Subtract(y \*BigUInt) (\*BigUInt, error)
    
    The Subtract method decreases `x` by the number represented by `y`, and returns a pointer to `x`. Note that `x`'s slice's size may decrease as a result of this operation. If `y > x`, then `Subtract` should return `(nil, ErrUnderflow)`, and _x should be left unchanged_ (this may take some care). As with `Add` above, `x`'s slice should have the minimum length necessary to represent the resulting number.
    

#### Testing

A large amount of testing code is provided in `biguint_test.go`. You do not need to modify this file for this assignment, but keep in mind that you will need to make your own tests in future assignments.
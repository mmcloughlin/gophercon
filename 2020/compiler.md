# How to Write a Self-Hosted Go Compiler from Scratch

## Compilers

* 8cc.go
* minigo
* babygo

## Arch

Produces the assembly. GCC does assembly and linking.

## Assumptions

* x86 and linux
* Lexer and parser are handwritten
* Stdlib from scratch
* Stack machine
* Far from production quality!


## Demo

Hello world

Go compiler produces 1.3M that runs in 12ms

* babygo produces assembly
* Assembled and linked with `as` and `ld`
* Produces 14K and runs in 5ms

## Self-Hosting

* Compile with official compiler -> 1st gen
* Use 1st gen to compile itself -> 2nd gen
* Use 2nd gen to compile itself -> 3rd gen
* The 3rd gen should be identical to 2nd gen

## Porting 8cc

Ported the 8cc compiler commit-by-commit to write a C compiler to Go.

## minigo

* Go easy to scan and parse.
* Parser can determine mode only by looking at one token
* Types read from left-to-right
* Few historical twists and turns

Therefore, following are easy:

* Lexer and parser
* Powerful tools like slice, map, range-for

Hard:

* Need to implement slice, map, for
* Data types are larger than a single register
* Handling on stack machine is not trivial
* Runtime:
    - gc
    - allocation

Learned the spec by writing a compiler.

Fighting with segmentation faults in the 2nd generation compiler.

## Implementations

* malloc: just bump allocator, no free
* map: linear search
* interfaces: also linear search

... lost track a bit, see slides :)

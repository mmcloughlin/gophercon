Small is Going Big: Go on Microcontrollers
Ron Evans


I am @deadprogram
Technologist for Hire
Son on stage with him :)

Open Source Work

    Gobot
    gocv
    tinygo


TinyGo

    Go for small places

    "Everyone says Go is too big"

    Programming languages often used for embedded systems:

        C, C++, Python!?, Rust

    But Go is too big!?

    Go can be tiny


Hello World!

    Using Go 1.12 vs. TinyGo

    Go 1.12 1.1MB
    TinyGo  12K


TinyGo is not the same thing as the full Go

    Most of Go, but not all of it.

    Does not support the whole standard library

    Already very useful!

    WebAssembly


How does it work?

    Go, TinyGo, LLVM

    Use go parser
    Convert to SSA
    Then convert SSA to IR
    Compile


Demo

    TinyGo on an 8K 8-bit processor!

    Very simple code for blinking an LED.

    Special "machine" package from TinyGo.


Demo

    GopherBot


Community Day

    Free Arduino Boards!
    Full day of hacking.


Internet of Things

    package net does not compile

    But... have compatible interface implementations.

    MQTT support.


Flight Controller

    "We can run goroutines! ... It's real I've seen it!"

    Flew a drone around the venue... with facial recognition on a camera!


RISC-V Demo

"The Future is Here Right Now!"


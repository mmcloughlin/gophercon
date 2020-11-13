# Tracing Go Programs with eBPF!

https://www.grant.pizza/talks/gophercon2020/

## Tracing

Inspect activity of running programs.

Make sure programs are doing what they say they're doing.

See why programs are not working properly.

Monitor performance.

## Example

### `strace`

Monitors for system calls

Built using `ptrace`

### `execsnoop`

Print out every time a program is executed.

## eBPF

Virtual machine inside the linux kernel

Write C code which is executed when triggered by events.

Compare to Javascript runtime inside a web browser.

eBPF does to the linux kernel what JavaScript does to HTML

Attach it to hooks.

Run in safe sandboxes.

"Made learning about the linux kernel so much more accessible."

## uprobes

Lets yu trigger eBPF

Attaches to symbols in binaries

## bcc

BPF Compiler Collection

Compiler/library for eBPF code

Invoke directly from Go

Easy to load and orchestrate eBPF code

## Demo

...


## Why

Logging and debugging of programs that are already compiled, deployed, or
running.

Sometimes you don't control source code.

## Tracing Go Function Arguments

Use eBPF to inspect stack.

Requires computing stack offsets and accessing memory via the stack pointer
(`SP`).


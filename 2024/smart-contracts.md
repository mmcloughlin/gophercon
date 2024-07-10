# Building a Deterministic Interpreter in Go: Readability vs Performance

...

GnoVM:

* Seamless language-level interop
* Fine-grained memory/CPU/storage limitations
* Automatic persistence
* Determinism

Stack-based AST VM. Not a bytecode interpreter.

About 100 opcodes. Each has fixed CPU cost. (What does that mean?)

All changes to values are tracked during exectuon. Persisted at the end of the
user's program.

## Lessons Learned about Go

Primitive types stored in interfaces are actually pointers.

Avoid switching on interface values when a type-switch would do.
Compiler knows ahead of time what the possible types are.

...

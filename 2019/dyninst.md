Dynamically Instrumenting Go Programs
Jason Keene


Intro

    BPF
    ptrace
    uprobes
    delve


Instrumentation

    Anything that allows you to make measurements

    Speedometer, smoke alarm, ...


Story

    Early Microscope designs

    Used early microscopes to study fibers in clothes he sold

    Also used to study water
    ... discovered bacteria

    Required to make progress in biology

    > A new kind of instrumentation was required to reach new understanding.


Instrumentation in Software

    Probe Effect

        Any time you measure a system, you're interacting with it

        There's usually overheads

    Categories

        Static and Always On

            Logging
            Metrics
            Distributed Tracing

            In the source code, always there.

        Static that requires activtion

            Leveled logging
            pprof
            usdt probes (dtrace?)

        Dynamic Instrumentation

            Debuggers
            Tracers
            uprobes/bpf

    Goals

        Tools that can answer arbutrary questions about our software

        Minimal overhead

    Streetlight Effect

        Only look where the light is.

        Analogy to static instrumentation.

        With dynamic instrumentation we can always look where the problem is?

        Need to be able to answer questions you don't even know until you have
        a problem.


Debuggers

    Use ptrace = "Process Trace"

    Suspend/Continue execution of another process
    Single-step
    Read/write memory
    Read/write registers

    How does it work?

    - insert 0xcc INT3 instruction at the breakpoint
    - interupt handler
    - debugger does what it wants
    - patches the instruction to replace the INT3
    - single-step
    - put the INT3 back
    - resume execution

    Delve has a JSON-RPC API.


Example

    Using the delve API to control a process.


Overhead

    Why are high frequency breakpoints so bad?

    Can we do better?

    Can we move it all into the kernel?

    Yes... uprobes + BPF.


uprobes

    kernel sets buffer for us

    SSOL buffer "single step out of line"

    uprobe handler invoked

    How do you write a uprobe handler?

    Kernel module?


BPF

    Custom instruction set.

    Attach to events.

    BCC compiles high level languages from C to BPF VM.

    Can setup data structures in kernel memory.

    Allows aggregation.


bpftrace

    Makes writing BPF programs straightforward.

    DSL

    No free lunch, but much better than before.


What if we could just write these in Go?

    goBPF

    Go bindings for libbcc.


Options

    delve
    bpftrace
    gobpf

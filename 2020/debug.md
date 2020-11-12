# Deterministically Debugging Go Programs

Extremely underutilized technique.

## Intro

What is Deterministic Debugging?

Groundhog Day movie example... Bill Murray goes to Punxatawney over and over
and over again. Starts every day exactly the same.

We want to be able to replay our programs, like Groundhog Day.

## How is this Possible?

How do we remove sources of non-determinism?

Record & Replay debugging.

Record the execution of a process in some way, such that we can faithfully
reproduce it an infinite number of times.

How is that actually possible? Under the hood: `ptrace`, `eBPF`, performance
counters.

## Process Recording

VM Recording:

* ReVirt
* VMware WorkStation
* Simics

User space recording:

* UndoDB
* TotalView ReplayEngine
* ...

### Problems

VM recording:

* Must debug within a virtual machine that supports this feature
* Produces very large traces (entire machine, all other processes, ...)
* Not widely supported, usually proprietary

User space:

* Does not record kernel execution
    - not that important
    - jist record the _results_ of kernel interactions
* Some require code instrumentation
* Some require kernel extensions


... this all sounds terrible.

## Solution

Solution (for now) is Mozilla RR.

Go defers to Mozilla RR for some of the recording and replaying.

Delve uses RR as a backend. Delve supplies all the unique Go knowledge.

## Eliminate Non-Determinism

Sources of non-determinism that we can control.

* Certain CPU instructions
    - mostly deterministic
    - `RDRAND`, `RDTSC`, `CPUID`
    - use trap to intercept and record
* Thread Scheduling
    - Mozilla RR ends up running things single-threaded
    - User space scheduler
    - Enables more faithful replay
* System Calls
    - file descriptors
    - memory mapping
    - "this one is pretty easy"
    - use ptrace
    - "a way to spy on other programs"
    - or, Mozilla RR inserts "shims" around syscalls
* Memory Layout
    - this can also be recorded and replayed
    - ensure variables and data are always in the same place
* Shared Memory
    - aleviated by Mozilla RR having its own thread scheduler
    - only one scheduled at a time
    - can be a performance hit
* Signal Handling
    - combination of ptrace and hardware performance counters
    - record when it was delivered
    - redeliver in replay

What about things we can't control:

* Code Executed Outside User-Space:
    - no kernel recording
    - limited to syscall recording (above)
    - treated as a black box
* Hardware Failures

## Evaluation

Pro:

* Pretty low overhead
* Can faithfully reproduce bugs (if you can record it once)
* Record trace and replay it anywhere
* Execute programs in _reverse_!
* Replay from beginning and execute same sequence of events

Con:

* Performance hit for highly parallel programs due to single threaded record
  and replay

## Demo

```
rr ./your-binary
dlv replay [trace directory]
```

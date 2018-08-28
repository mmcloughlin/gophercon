Self Deploying Kubernetes Applications - Kelsey Hightower
======

Go lets us produce self-contained binaries. What are some methods for deploying those?

# What does it mean to deploy to a single machine?

1) Compile the program
2) Copy the program to the target system
3) Start the program
- load the program into memory
- kernel takes over and passes control back to program

No matter how much we wish, prod will never be our laptop.

>> Kubernetes' application-centric abstractions allow us to treat a single machine like any machine

["Bash is why people leave IT," Kelsey says to raucous laughter]


# Deploying to many machines?

1) Compile the program
2) Build an application container image
3) Write some .yaml

# Self-Deploying Applications

1) Compile the program
2) Start it:
- Load the program into memory
- Kernel takes over and passes control to the program

# Demo

[Much of what Kelsey demonstrates here are various features of Kubernetes]

["I left a lot of time for Q&A, so ask good questions. If it's boring from here on out, that's on you"]

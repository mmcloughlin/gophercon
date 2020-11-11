# The Quest for the Fastest Deployment Time

L Körbes

## Developer Workflow

Key: Short Feedback Loop

Go addresses the absurd compile times of C++.

Go has achieved that goal, but...

## But...

There's more to the feedback loop than compile time.

Looking at you, Kubernetes.

Unscientific poll, near half of respondents said 30 minutes or more.

## Three Important Rules

* Don't rely on CI for development feedback
* Use an BDX tool like Tilt, Skaffold, Telepresence, or Garden
* Then use every hack, cheat, and dirty trick in the book

## Worked Example

Use a **Layer Cache** to Speed up Downloading Dependencies and Building.

Alternative: **Vendor Dependencies**, but don't have issue where a single
change causes entire rebuild.

Use **Compiler Cache** inside the container.

Also use **Live Update**, and **No Debugging** information.


# Vulnerability Management for Go

What is a vulnerability?

Causes a weakness that can be exploited causing negative impact.

The exploitation for negative impacts is what separates them from regular old
bugs.

## Supply Chain Attacks

Rise in supply chain attacks.

740% growth in the last few years.

"At least in the US, the president told us to!"

Executive order. Vital to national security.

## Supply Chain

Cake: ingredients, preparation, tins, oven, ...

What if you have an allergy? What if you want a gluten free cake?

Same concepts to code.

* Code
* Tools used in build process
* Dependencies
* Deploy process

"How can we be so sure the sofrware we are shipping is not vulnerable to
attack?"

We need to make sure the software supply chain is protected.

Today, focus on finding vulnerabilities in our dependencies.

"We all download code from complete strangers on the internet"

## Go Modules and Scanners

`go.mod` file: direct and indirect dependencies

There are already many tools out there that support this format.

Example: Dependabot

We wanted to make this better for Go developers.

There's the cry wolf problem. Reporting lots of CVEs that look scary but might
not actually be relevant.

## Goals

1. Comprehensive and high-quality data
2. Low noise alerts
3. Widely available tools

## Data Sources

* Github advisories
* National vulnerability database
* Go Project Vulnerabilities
* Go package maintainers

Aggregate into the Go vulnerability database

Each one reviewed and curated. Also add information about **affected symbols**.

## Low-Noise with Static Analysis

**Track vulnerabilities at the call-graph level!**

Static analysis to only report the vulnerabilities that might be relevant.

## Tooling

`govulncheck` uses static analysis to see what vulnerable functions are
actually being called.

Example of 15 CVEs down to 2.

Also provided a _Github Action_.

Also exposed in the Go package site. Red badges expose bugs in versions.

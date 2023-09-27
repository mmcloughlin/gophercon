# Idiomatic Go Tells a Story

Writing code like "Telling a story"

* Title
* Scene setting
* Introduction
* Character intro
* ... actions
* ... intro

Code:

* Package name
* Imports
* Initializations
* Type definitions
* Functions involving type
* ...
* Type definitions
* Functions involving type
* ...

## Characters

Main Characters are the **types** you define in your package, taking action
throughout your code.

Template: `world.Noun.Verb`.

## "Paragraph Structure"

Read function defintions almost like a sentence.

```
func (t *TheThing) TakesAction(with int) {
    ...
}
```

> The name of a method's receiver should be a reflection of its identity;
> often a one or two letter abbreviation of its type suffices (such as "c" or
> "cl" for "Client"). Don't use generic names such as "me", "this" or "self",
> identifiers typical of object-oriented languages that gives the method a
> special meaning.

https://github.com/golang/go/wiki/CodeReviewComments#receiver-names

## Single Use Variables

```go
t := time.Now()
conf, err := c.Query(t)
```

Reads like a "blip".

* "The time is September 27th"
* We had Gophercon at the time

How about?

```go
conf, err := c.Query(time.Now())
```

* "We had Gophercon on September 27th"

"If you are taking one action, it can often be idiomatic to put it on one
line"

## Interfaces

> Go interfaces generally belong in the package that uses values of the
> interface type, not the package that implements those values. The
> implementing package should return concrete (usually pointer or struct)
> types: that way, new methods can be added to implementations without
> requiring extensive refactoring.

https://github.com/golang/go/wiki/CodeReviewComments#interfaces

"Main characters need to solve a problem and they need some help"

You say "I need help, in this exact way"

You don't want them to be driving the main story, they're supporting
characters.



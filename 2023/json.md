# The Future of JSON in Go

Natural mapping between JSON and Go types.

`json.Marshal` and `json.Unmarshal`

How about custom Go types?

* `encoding.Text{Marshaler,Unmarshaler}` (applies to other formats too)
* `json.{Marshaler,Unmarshaler}`

## What's wrong with JSON `v1`?

Over a decade old. "overall held up pretty well"

Number 5 top imported Go packages.

"We should tread very carefully"

There will always be a lot of code that relies on the current package.

We cannot break compatibility since that's one of the hallmarks of the

### 1. Missing Features

92 open issues

Wouldn't strictly require a v2.

### 2. API Deficiences

Require a breaking change.

Main: inability to specify options to marshal or unmarshal, and inability to
plumb them through to custom marshalers.

Options only available on `Encoder` and `Decoder`, but then you have to use
the `io` types instead of `[]byte`

No easy way to unmarshal from `io.Reader`.

### 3. Performance limitations.

* `MarshalJSON` must allocate (returns a `[]byte`)
* `MarshalJSON` and `UnmarshalJSON` require a _second parse_
    - After calling `MarshalJSON` the `json` package has to validate the
      returned buffer
    - Before calling `UnmarshalJSON`

The "double parsing" effect can cause **quadratic** behavior.

Example: parsing a `[[[[...]]]]` nested array.

It's synthetic, but complex data types do often call `json.Unmarshal`
recursively in their `UnmarshalJSON` methods. It can happen for real.

### 4. Behavioral Flaws

`MarshalJSON` and `UnmarshalJSON` called inconsistently.

"Not called when expected to be called"

It's arguably a bug, but can't be fixed because of Hyrum's Law (they tried).

Unmarshaling allows invalid UTF-8, which violates the RFC.

Unmarshaling allows for duplicate names.

Unmarshaling matches keys case insensitively.

These have real _security implications_. Showed an example of a privilege
escalation exploiting duplicate keys.

With duplicate keys, JSON messages have ambigious meanings. They should be
rejected by default.

## `v2` proposal

"Might never see the light of day"

High level:

* `jsontext` manipulates encoding/decoding JSON as syntax. Minimal
  dependencies. No reflection. (Works for TinyGo.)
* `json` provides mapping between JSON and Go types

### `jsontext`

`Token` and `Value` types

Functional options pattern.

### `json`

Still support the V1 marshal/unmarshal methods.

New V2 marshal interface.

Generic functions that allow overriding marshalling for external types.

...

Options added to the V1 JSON package.

These allow for bug-for-bug compatibility with V2.

There's a "gradient" of options you can change to get from V1 to V2.

Options are flexible enough to force the V2 package to behave _exactly_ like
V1 (with `DefaultOptionsV1`).

## Notable changes

* Invalid UTF8 rejects
* Dupe names are rejected
* JSON objects names use case sensitive match
* Nil slices are marshaled as empty JSON array
* Nil maps marshalled as empty JSON object

Tags:

* `omitempty` is redefined based on the JSON type system, not the Go one.
  Omitted if it would be marshalled as an empty JSON type (`null`, `""`, `[]`, `{}`)
* `omitzero` omits a struct field that is the zero Go value
* `inline` is the JSON equivalent of Go struct embedding
* `format` specifies special JSON format for certain Go types

## Performance

Generally parity in Marshal performance.

Unmarshal performance is 2.5x to 8x faster!

(Does not use unsafe at all.)

Critical that untrusted inputs cannot cause security bugs.

Optimized parser implementation.

## What's Next?

Nothing set in stone.

Stay tuned for Github discussion.

Want to make sure the interop story is good.

Eventually a formal proposal.

A few releases at minimum.

There is a real prototype. Pretty confident in the implementation.

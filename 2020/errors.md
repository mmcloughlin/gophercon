# Working with Errors

* People use errors todiagnose and debug
* Programs: retry, try alternative, better error messages for humans

## Problem

Before Go.13, code that inspected error objects or error types would be broken
if errors were wrapped.

## Now

Wrap with `fmt.Errorf("... %w")`.

Replace `==` with `errors.Is`

Replace type checks with `errors.As`.

## Under the hood

Any error can define `Unwrap() error` method.

* `errors.Unwrap` unwraps a single error
* `errors.Is` looks for the exact error in the unwrap chain
* `errors.As` looks for a matching type in the unwrap chain
* `fmt.Errorf("... %w", err)` returns an error wrapping `err`

## Migrations

* Start using errors.Is/As
* Not necessary if explicit return is documented

Note library authors could break clients by using wrapping.

Don't wrap pseudo-errors like `io.EOF`

## Features that Didn't Make It

### Stacktraces

* Too much detail
* Not enough information
* Multiple copies
* Goroutines

Tried adding to `errors.New` slowed down a lot of programs.

Alternative: essentially build the stack trace yourself.

```
defer func() {
    if err != nil {
        err = fmt.Errorf("processZip(%q, %q): %w", path, version, err)
    }
}()
```

Refactor into a helper:

```
func Wrap(errp *error, format string, args ...interface{}) {
    if *errp != nil {
        s := fmt.Sprintf(format, args...)
        *errp = fmt.Errorf("%s: %w", s, *errp)
    }
}
```

Then it's just:

```
defer Wrap(&err, "processZip(%q, %q)", path, version)
```

### Detail Formatting

https://github.com/jba/errfmt

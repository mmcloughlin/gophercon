Go Reliability and Durability at Dropbox

TL;DR;

  - This talk was kind of "fluff"
  - Recruitment talk
  - Dropbox has complex infrastructure supported by Go, but this talk was
    light on details
  - Great to know other large companies are "all in" on Go

Magic Pocket

  - in-house storage system

Onboarding

  - Project for developer directory with the applications they work on
  - Bazel for builds
  - Mandatory testing
  - Onboarding project is shipped
  - About a week for experienced engineers, longer for others
  - Ramp up interns with "Hack night" where they can work on whatever they
    want in the evening

Difficulties

  - Race conditions
  - Data race detector doesn't always help

Next

  - On 1.6. Skipping 1.7, going to 1.8
  - Every new engineer writes Go first
  - Also use Python and (some) Rust

# Go Changes

Decision and Consensus: "... **if they have the same goals** then they're
likeky to reach the same conclusion"

We have to establish what Go's goals are.

Go's goal is **better software engineering**, especially at scale.

"I love Perl for what it is, but it's not aiming for better soft

## Sofware Engineering

... is what happens to programming when you add time and other engineers.

What happens when the code has to work day after day when other engineers add
features.

Or you, siz months from now.

Therefore:

* Emphasize testing package and go test
* Long term _compatibility_ promise
* `gofmt` to unlock automated editing tools
* Unambiguous import paths (hence URLs)
* Go modules, workspace, mirror, checksum database
* Go vulnerability database, govulncheck

Also:

* Updating protocols
* Removing support for insecure systems
* Fixing and deprecating APIs

## Proposal Process and Data Driven Decisions

Anyone can file a Go change proposal.

Consesus is likely when the people involved have "the same information".

The best source we've found is to **gather actual data**.

For example:

* Talking to Go users. The Go user survey. vscode Go survey. Also research
  interviews and user experience studies.
* Open source code analysis. Especially developing lint/vet checks.

"Magic of sampling."

It's worth spending time 

Sampling turns many one off estimation into jobs that are possible by hand,
and give good accuracy.

### Telemetry

Should be a **representative** sample, and answers different quesitons than
surveys and code analysis.

We get:

* Detailed information that you can't ask for in surveys
* Breakage information

Design: "counters and crashes" reported weekly.

The collected counters are agreed in a public proposal process, and all the
past upload configurations are archived and public.

Sharing:

* No sensitive data
* We can publish data in full
* Also publish the analysis

Will enough people opt-in?

* Because of the "magic of sampling" we don't need that many
* We can even tune down the upload rate if we get enough

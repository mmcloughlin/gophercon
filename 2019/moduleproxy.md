The Athens Project - A Proxy Server for Go Modules
Aaron Schlesinger


Breakages

    Delete tag
    Delete repo
    Rename repo
    Github unavailable


Athens Project

    The Module Download API

    Core of the solution

    Small part of the spec, massive impact


Module Download API

    Metadata endpoints

    Get module files for a repo

    Download zip file -- NOT a repo

    This is "Our API"... the Go communities

    We can build implementations.


Implementation

    API server is an abstraction over various hosting providers

    Now `go get` uses the API


So now we have a registry

    Discoverable

    eg. npm breaking


Athens

    Another API server

    Doesn't replace proxy.golang.org

    It can also talk to other API servers

    Point `go get` at Athens

    Stores code in storage you control.
    No layer of abstraction.
    YOU control the code you rely on.


Demo

    Building with local Athens

    Then purging cache, shutting off internet.

    Build works with local storage.


Why would you actually run your own Athens

    1. Privacy

        Serve up private repos.

    2. Auditing

        You know every dependency the company has.
        Audit for eg. license issues.

    3. Isolation

        Shut off your org from the internet.


Decentralized

    Let's not lose this!

    APIs stay open because of participation.


Call to Action

    Run Athens

    Keep Modules Decentralized


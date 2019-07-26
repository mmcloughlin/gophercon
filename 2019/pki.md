PKI for Gophers
Eric Chang


Lots of code, notes not easy for much of it.


Intro

    TLS on the uptick

    Soon, everything will be encrypted!


Signatures

    ecdsa

    Certificate = signed public keys with extra information



Certificate authorities

    Setup your own in Go


...

The Punchline

    TLS is easy
    PKI is hard!


Certificate Signing Request

    Hey, I have a key, will you sign it.

    No private information shared.

    Easy in Go with x509 package.


Sending CSRs

    Include JWT that attests to the identity of the requesting servers.


Revoking a Certificate

    Revokation list from a certificate authority.

    Use the pkix.RevokedCertificate in Go.


OCSP Stapling

    ...

Rotate a CA?

    Include the new cert in a certificate bundle.

SNI

    Host multiple websites behind the same endpoint.

    SNI = server name identification

    Go allows you to offer dynamic certificates by connection.


    Plain Text ... anyone inspecting the connection can see it

    Government of Kazakstan is using this!

    Encrypted SNI... no support in Go yet.

    "Well thought out proposal, but a lot of fallout."


Client Authentication

    Use CommonName field.


Hardware Keys

    Private Key storage.

    Not a perfect solution.

    crypto.PrivateKey is an empty interface

    The ykpiv package can be used to interact with a yubikey.

    "Never assume your keys are always in memory"


Let's Encrypt

    Huge reason why the internet is heading towards ubiquitous TLS.

    Issues a challenge. Prove you can host this given long string.

    Signs a free certificate, trusted by browsers.

    Go has a package called autocert.


Certificate Transparency

    Integral part of Chrome today

    Evil CA issues bad certificate.

    How does anyone know?

    Cert pinning limits the risk.

    CA signings should be public record. Certificate transparency log is a
    record of every certificate. Cryptographic log. Prove it hasn't been
    tampered with.


    Certificate transparency library in Go.


Closing Thoughts

    "Just use TLS"


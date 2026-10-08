# h-rplca local TLS fork

This local development fork starts at ianic/tls.zig's `zig-0.16.x` commit
`ba3008f7d6750b528a4641e311816ad65c3ca426`. The `upstream` remote tracks
https://github.com/ianic/tls.zig; no hosted fork has been published.

Branch: `codex/peer-key-pinning`.

The initial patch adds optional `expected_peer_key` (algorithm plus borrowed
public-key bytes) to client/server options. It supplements certificate-chain,
CertificateVerify and Finished verification. A different leaf key fails with
`TlsPeerKeyMismatch`, including a leaf signed by a configured trusted peer.

Pinning currently requires a full verified handshake: client insecure mode and
session resumption are rejected when a pin is set; server client-auth must be
`require`. Expected key storage must outlive the handshake. The implementation
is an experimental paired-peer interface. A shared listener's authenticated-key
result and nonblocking invalid-options handling need further work before an
upstream proposal. No TLS exporter or bulk-ticket protocol is implemented here.

Run the upstream unit suite with:

```sh
zig build test -Doptimize=ReleaseSafe --summary all
```

The h-rplca INFRA-521 worktree consumes this directory as its local `tls` package.
Its `test-tls-audition` target tests real loopback connections with exact-key
pinning required on Threaded and Zio. It does not yet replace h-rplca's OpenSSL
`Session` implementation.

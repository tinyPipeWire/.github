# Security policy

## Reporting a vulnerability

Report it privately through GitHub, using **Report a vulnerability** on the
Security tab of the repository concerned:

- [tinypipewire](https://github.com/tinyPipeWire/tinypipewire/security/advisories/new)
- [tinypipewire-rs](https://github.com/tinyPipeWire/tinypipewire-rs/security/advisories/new)

That opens a draft advisory only you and the maintainers can read. Please use
it rather than a public issue, so a fix can go out before the details do.

Useful things to include: which version or commit, whether the C library or
the Rust crates are affected, and the smallest program that shows the
problem.

## What to expect

These are small projects with a single maintainer, so treat any timeline as
best-effort rather than a guarantee. You should get an acknowledgement within
a week. If a report leads to a fix, the advisory is published with it, and
you are credited unless you ask otherwise.

## Scope

Both projects wrap PipeWire and hand out pointers into buffers the graph
owns. Memory-safety bugs, a safe Rust API that permits undefined behaviour,
and anything that lets a stream read or write outside its own buffers are all
in scope.

A vulnerability in PipeWire itself is not; report those to
[the PipeWire project](https://gitlab.freedesktop.org/pipewire/pipewire). If
you are unsure which side a bug sits on, report it here and it will be passed
along.

## Supported versions

Only the latest release of each project is supported. There are no
maintenance branches, so fixes land on `main` and go out in the next release.

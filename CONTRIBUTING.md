# Contributing

These notes cover both [tinypipewire](https://github.com/tinyPipeWire/tinypipewire)
(the C library) and [tinypipewire-rs](https://github.com/tinyPipeWire/tinypipewire-rs)
(the Rust bindings). Anything a repository needs to say differently, it says
in its own README.

Both target Linux, because PipeWire does. You need a running PipeWire daemon
to run the tests: they connect real streams rather than mocking the graph.

## Building and testing

The C library builds with Meson:

```sh
meson setup build
meson compile -C build
meson test -C build
```

`meson test` needs no hardware. Tests that do — they link a real camera,
microphone or sensor into a filter — and tests that measure timing live in a
separate suite, and report SKIP rather than failing when the device is
absent:

```sh
meson test -C build --suite hardware
```

The Rust bindings build with Cargo, against either an installed C library or
the copy pinned as a submodule under `tinypipewire-sys/vendor/`:

```sh
git clone --recurse-submodules https://github.com/tinyPipeWire/tinypipewire-rs
cargo build --features vendored
cargo test --workspace --features vendored
```

The committed bindings have to match what bindgen produces today, or the
`bindings` job fails. Two things move them: a change to the C headers, and a
change to the bindgen version itself, since a new release can generate
different code from identical headers. Regenerate either way:

```sh
TINYPIPEWIRE_SYS_UPDATE_BINDINGS=1 cargo build -p tinypipewire-sys
```

On a machine with no PipeWire, add `TINYPIPEWIRE_SYS_HEADERS_ONLY=1` to read
the submodule's headers and skip linking.

Dependabot raises the bindgen requirement but never regenerates, so its pull
request needs that command run on its branch before it can merge. bindgen
0.73 dropped `Copy` and `Clone` from the opaque handle types, which is the
shape this takes: mechanical, and worth reading before accepting.

## Sending a change

`main` is protected in both repositories: it takes no direct pushes, and a
pull request merges only once CI is green. Branch from `main`, open a pull
request, and let the checks run. Merged branches are deleted automatically.

CI runs the full test suite, and for the C library also builds it under
AddressSanitizer and UndefinedBehaviorSanitizer. A pull request that changes
a public C signature gets a comment saying so — that is a prompt to check the
change is deliberate, not a failure.

For the Rust crates, one job packages `tinypipewire-sys` and builds the
extracted copy. That is what catches a build reaching for a file the
published crate would not carry, so keep anything the build reads inside the
crate directory.

## Commit messages

Subjects are `area: Imperative sentence`, where the area is either the kind
of change (`build`, `ci`, `docs`, `tests`, `examples`, `api`) or the
subsystem touched (`stream`, `filter`, `video`, `utils`, `sys`). No trailing
period.

```
docs: Correct what the routing setters actually refuse
video: Add H.264 encoded video format support
```

Write the body in full sentences, and use it to say why the change is right
rather than to restate the diff. Keep it short.

## Documentation

Public C headers carry Doxygen comments, and a test enforces their shape:
`@brief` on everything, `@return` on every non-void function, `@param` names
matching the declaration, out-parameters after in-parameters, and every
`@see` resolving to a real symbol. `meson test` runs that check, so a header
that drifts fails the build like any other test.

## Versioning

The C library and the two Rust crates version independently, because a change
to the Rust interface and a change to the C API are different events.
`tinypipewire-sys` names the C release it pins as build metadata, as in
`0.1.0+tpw0.9.1`.

**Moving the pinned C release bumps the crate version too.** Build metadata
is ignored when versions are compared, so `0.1.0+tpw0.9.1` and
`0.1.0+tpw0.10.0` are the same version to Cargo and to crates.io, and the
second could never be published. Raise the patch alongside the pin, the way
`libgit2-sys` goes `0.18.7+1.9.6` then `0.18.8+1.9.7`. The metadata records
which C release a crate carries; it never distinguishes two releases on its
own.

Changing the pin also means regenerating the committed bindings, since the
`bindings` job compares them against the pinned headers.

## License

Contributions are accepted under the MIT license, matching both projects.

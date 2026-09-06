<!--
Subject line: `area: Imperative sentence`, where area is the kind of change
(build, ci, docs, tests, examples, api) or the subsystem touched (stream,
filter, video, utils, sys). See CONTRIBUTING.md.
-->

## What this changes

<!-- What the code does now that it did not before, and why that is right. -->

## Why

<!--
The reasoning, not the diff. If this fixes something, say what was wrong
rather than which lines moved.
-->

## Checks

- [ ] `meson test -C build` passes, or `cargo test --workspace --features vendored` for the Rust crates
- [ ] New behaviour has a test, or there is a reason here why it cannot have one
- [ ] Doxygen comments updated on any public C header touched
- [ ] Committed bindings regenerated, if this changed the C headers

<!--
Two things CI will tell you about, so they need no checkbox:
- a changed public C signature gets a comment on this PR, which is a prompt
  to confirm it was deliberate rather than a failure
- the C library also builds under AddressSanitizer and UndefinedBehaviorSanitizer
-->

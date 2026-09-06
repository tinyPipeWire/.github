# tinyPipeWire

PipeWire exposes a lot of surface to an application that only wants to move
audio or video: a thread loop to drive, SPA POD format negotiation to get
right, and buffers to dequeue and queue by hand. These projects put a small
opaque-handle API in front of that.

| Repository | Language | What it is |
| --- | --- | --- |
| [tinypipewire](https://github.com/tinyPipeWire/tinypipewire) | C | The library. Wraps `pw_stream` for audio and video capture, audio playback, and multi-port filters. |
| [tinypipewire-rs](https://github.com/tinyPipeWire/tinypipewire-rs) | Rust | Bindings to it — raw FFI in `tinypipewire-sys`, owned handles and `Result` in `tinypipewire`. |

## Where to start

Write C, and you want the library directly: it builds with Meson and needs
`libpipewire-0.3` >= 0.3.50 development files.

```sh
meson setup build && meson compile -C build
```

Write Rust, and you want `tinypipewire-rs`, which either links an installed
copy of the C library or builds the vendored one it pins as a submodule.

```sh
git clone --recurse-submodules https://github.com/tinyPipeWire/tinypipewire-rs
cargo build --features vendored
```

Either way the target is Linux, because PipeWire is.

## Scope

Audio and camera capture are supported, as is audio playback. Video playback
is deliberately absent: PipeWire has no video sink to play into, so an
application that wants to emit video becomes a source node instead — which is
what a filter's output port already is.

The two Rust crates carry their own semver rather than the C library's,
because a change to the Rust interface and a change to the C API are
different events and each needs a version to say so.

Everything here is MIT licensed.

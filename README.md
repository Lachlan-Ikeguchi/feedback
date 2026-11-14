# feedback

Feed the default microphone back through the default speakers, live.

A small command-line tool for PipeWire systems (KDE Plasma, GNOME, etc. on
any modern Linux desktop). The loop uses exactly the devices the desktop
environment has selected — the PipeWire default source and sink — and
follows the selection when you switch devices from the DE's sound settings.

## Usage

```console
$ feedback start                  # mic -> speakers, live
$ feedback status                 # state + devices in use
$ feedback toggle                 # start if stopped, stop if running
$ feedback stop                   # stop the loop

$ feedback start --latency 10     # aim for ~10 ms stream latency
$ feedback start --delay 0.5      # half a second echo
```

## How it works

The script manages a single `pw-loopback` (PipeWire's native loopback client)
process:

- No capture or playback target is set, so PipeWire links it to the current
  default source (the DE's selected microphone) and default sink (the DE's
  selected speakers/headphones).
- Because the targets are not pinned, WirePlumber re-links the loop when the
  default device changes — switch devices in the DE and the loop follows.
- The process is detached; its PID is kept in
  `$XDG_RUNTIME_DIR/feedback.pid` and its output in `feedback.log` there.

## Install

```console
$ install -Dm755 feedback ~/.local/bin/feedback
```

(make sure `~/.local/bin` is on your `PATH`), or run it straight from this
directory: `./feedback status`.

## Dependencies

- `pw-loopback` — the tool itself (PipeWire)
- `pactl` — only for `status` output (pulseaudio-utils)

## Warnings

- With open speakers near the microphone this creates an acoustic howl
  (that is what a feedback loop is). Best used with headphones.
- `--delay` buffers audio in PipeWire; long delays use some RAM
  (defaults are sane, `--delay 2` is roughly 380 KB).

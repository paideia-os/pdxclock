# pdxclock — CHANGELOG

Semver-tagged release history. Every entry corresponds to a git tag on
this repository.

## 0.5.0 — 2026-09-13 — M1..M4 landing (Closes #1. Closes #2. Closes #3. Closes #4. Closes #5.)

First code landing. Lands R102.M1-001 through M4-001 in one batch per
design/graphics/r102-user-plan.md §2.7 / §3.7 / §4.7 (paideia-os
monorepo):

- **#1 (M1-001 repo scaffold)** — `caps.decl` (KIND_USER,
  KIND_IPC_ENDPOINT, KIND_SURFACE, KIND_CLOCK), `tools/build.sh`
  (paideia-as resolution + per-file `--emit elf64` verify, mirrors
  `tools/user/mv`/`rm`'s unlinked build shape), this CHANGELOG,
  `manifest.pdxsig` source-form. README/LICENSE predate this tag.
- **#2 (M1-002 caps.decl + argv + window)** — `src/argv.pdx`
  (`--x=<pos>` / `--y=<pos>`, default 0,0), `src/window.pdx`
  (`Window::window_open` — requests a 128x48 ARGB32 window from
  svc.compositor over a provisional pdxclock-local IPC envelope;
  svc.compositor is not yet a landed satellite, so this fails closed
  with -ESRCH today by design — see the file header), `src/tool_ident.pdx`.
- **#3 (M2-001 HH:MM:SS render + 1 Hz loop)** — `src/render.pdx`
  (`Render::format_hms` decomposes `sys_clock_read_ns`'s ns-since-boot
  reading as `(ns/1e9) mod 86400` into hh:mm:ss; `Render::blit_stub`
  emits the 8-char result over the debug channel as a WEAK stand-in
  for the not-yet-landed libpdx-gfx/libpdx-font pixel path;
  `Render::render_run_tick` enforces the ~1 Hz cadence via a
  clock-delta busy-wait, since paideia-os has no sleep syscall at
  HEAD).
- **#4 (M3-001 WindowRecord CLOSING handling)** — `src/close.pdx`
  (`Close::close_poll` — non-blocking per-tick poll for a CLOSING
  WindowRecord; sends a best-effort DETACH to the compositor and
  calls `sys_exit(0)` on receipt).
- **#5 (M4-001 seeded-time smoke)** — `tests/pdxclock_seeded_time_smoke.pdx`
  (`PdxclockSeededTimeSmoke::run` calls the real `Render::format_hms`
  against the seed `1_700_000_000_000_000_000` ns and asserts
  `"22:13:20"` — the arithmetically correct decomposition; the
  issue's suggested `"16:53:20"` does not hold under either the
  ns-since-boot or Unix-epoch reading of the seed, and the issue text
  allows substituting "the correct H:M:S decomposition").
- `src/pdxclock.pdx` — `Pdxclock::_start`, wiring argv/window/render/
  close together into the M1..M4 loop described above.

Substrate posture: svc-compositor, svc-wm, libpdx-gfx, libpdx-font,
and libpdx-event are all unlanded satellite repos at this tag (per
r102-user-plan.md §3.1-§3.4); every provisional wire choice this
release makes is documented at its call site with the exact one-
function swap a future landing needs. M4-002 (close-on-WM-quit smoke)
and M5-001 (signed 1.0.0 release) remain open.

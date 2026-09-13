# pdxclock

The **smallest useful window app** on paideia-os. Renders `HH:MM:SS` on a 128x48 window, updates once per second, closes on `WindowRecord` `CLOSING`. Used as the actor in the `boot_r102_window_present` smoke — the first-window-on-screen demo.

Part of the **paideia-os** organization. MIT-licensed.

## Wave

R102 (softarch userland graphical stack) — the CPU-side framebuffer stack
that lands the first graphical UI on paideia-os before the G-series
GPU-accelerated compositor matures. Companion to the osarch R101 kernel-side
plan.

## Design reference

- Design lives in the monorepo at [`design/graphics/r102-user-plan.md`](https://github.com/paideia-os/paideia-os/blob/main/design/graphics/r102-user-plan.md) §2.7 / §4.7.
- Kernel-side companion: `design/graphics/r101-kernel-plan.md`.

## Milestones

Per the plan, this repo lands across five milestones:

- **M1** — repo scaffold; caps.decl (KIND_SURFACE, libpdx-event, monotonic clock); argv parser (--face); 128x48 window creation
- **M2** — HH:MM:SS render + 1 Hz loop over libpdx-gfx + libpdx-font; full-window damage per tick
- **M3** — WindowRecord CLOSING handling via libpdx-event
- **M4** — smokes: seeded-time render, close-on-WM-quit
- **M5** — signed 1.0.0 release

Every issue is filed against one of these five milestones; see the Issues tab.

## Status

v0.5.0 lands M1-001 through M4-001: `caps.decl`, argv (`--x=`/`--y=`),
window-open request against svc.compositor (`src/window.pdx`), the
HH:MM:SS render loop (`src/render.pdx`), WindowRecord CLOSING handling
(`src/close.pdx`), and the seeded-time regression
(`tests/pdxclock_seeded_time_smoke.pdx`). svc-compositor, libpdx-gfx,
libpdx-font, and libpdx-event are not yet landed satellites, so the
window-open handshake speaks a documented provisional wire and the
render "blit" is a WEAK debug-channel stub — see each file's header
for the exact one-function swap a future landing needs. M4-002
(close-on-WM-quit smoke) and M5-001 (signed 1.0.0 release) remain
open.

Repo shape mirrors R100 satellites: `caps.decl` at root, `src/` module
tree, `tests/`, `manifest.pdxsig` (source-form; real signatures land
at M5-001).

## License

MIT. See [LICENSE](LICENSE).

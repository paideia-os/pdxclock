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

`v1.1.0` is the Track C release closer (pdxclock#10) on top of
`v1.0.0-src`: no `src/` or `tests/` file changes, just the version
bump, this Status section, `CHANGELOG.md`'s `[1.1.0]` entry, and the
`manifest.pdxsig` re-stamp. All five R102 milestones remain landed as
of `v1.0.0-src`: `caps.decl`, argv (`--x=`/`--y=`), window-open request
against svc.compositor (`src/window.pdx`), the HH:MM:SS render loop
(`src/render.pdx`), WindowRecord CLOSING handling (`src/close.pdx`),
the seeded-time regression (`tests/pdxclock_seeded_time_smoke.pdx`),
and the close-on-WM-quit smoke (`tests/pdxclock_wm_quit_smoke.pdx`).
svc-compositor, libpdx-gfx, libpdx-font, and libpdx-event are still not
yet landed satellites, so the window-open handshake speaks a documented
provisional wire and the render "blit" is a WEAK debug-channel stub —
see each file's header for the exact one-function swap a future
landing needs.

This remains a SOURCE-FORM release: `manifest.pdxsig`'s dual
ML-DSA-65 signature blocks are `PENDING` pending paideia-as v0.33's
crypto intrinsics, the svc.signing-bot broker, and the pkgs.paideia-os
host (T-INFRA-001/002) — `pkg install --strict` refuses this package
until a release runner fills both blocks in. Same posture every other
satellite in this org ships pre-v0.33; the tag is a bare `v1.1.0` per
the naming convention this org's other post-1.0 satellites (e.g.
pdxtrust) now use, rather than an `-src` suffix.

Repo shape mirrors R100 satellites: `caps.decl` at root, `src/` module
tree, `tests/`, `manifest.pdxsig` (source-form; real signatures land
once paideia-as v0.33 crypto + the release-runner infra are reachable).

## License

MIT. See [LICENSE](LICENSE).

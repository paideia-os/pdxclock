# pdxclock — CHANGELOG

Semver-tagged release history. Every entry corresponds to a git tag on
this repository.

## 1.2.0 — 2026-09-13 — v1.1-B semantic-pipe emission wire (Closes #9.)

Lands on top of the `v1.1.0` Track C release closer (below) — bumped
to 1.2.0 rather than 1.1.0 since `v1.1.0` is already tagged in this
repo's history for the docs-only #10 close:

- **#9 (v1.1-B semantic-pipe emission wire)** — `src/pipe_emit.pdx`
  (`Module PipeEmit`): `pipe_emit_send(ns_since_boot)` marshals a
  128-byte `ClockTickRecord@0.1` (version/op, hour/minute/second
  decoded via the same div-by-constant decomposition `Render::format_
  hms` uses, the raw pre-mod `ns_since_boot` reading, an `rdtsc`
  timestamp) and emits it via `sys_semantic_send` (sysno 115).
  `Render::render_run_tick` (`src/render.pdx`) now calls `pipe_emit_
  send` once per rendered tick, right after updating `_render_last_
  tick_ns` — return discarded, best-effort, matching `pdxpaint`/`cat`/
  `cp`'s own emission posture. `manifest.pdxsig` bumped to package
  version 1.2.0 / git tag `v1.2.0` (bare, no `-src` suffix, per the
  naming convention #10's Track C closer established below),
  `content_hashes` extended to cover `src/pipe_emit.pdx`, and
  `declares_output_schemas` (both `caps.decl` and the manifest) now
  listing `ClockTickRecord@0.1`.

## 1.1.0 — 2026-09-13 — Track C release closer (Closes #10.)

Docs/release-only bump on top of `v1.0.0-src`: no `src/` or `tests/`
file changes. Part of Track C's v1.1 pass across 12 mature satellites
in the paideia-os org (see the monorepo's MASTER_PLAN.md for org-wide
milestone alignment).

- **#10 (v1.1-C release closer)** — `manifest.pdxsig` re-stamped:
  `package.version` 1.0.0 → 1.1.0, `git_tag` `v1.0.0-src` → `v1.1.0`
  (bare, no `-src` suffix, per the naming convention this org's other
  post-1.0 satellites now use). Both dual ML-DSA-65 signature blocks
  (§6 author, §7 root) remain `PENDING` — real signing still needs
  paideia-as v0.33's crypto intrinsics, the svc.signing-bot broker,
  and the pkgs.paideia-os host (T-INFRA-001/002), none of which are
  reachable yet. README's Status section updated to describe the
  v1.1.0 release-closer scope.

## 1.0.0-src — 2026-09-13 — M4-002 + M5-001 (Closes #6. Closes #7.)

Closes out the R102 plan's five milestones. This is a SOURCE-FORM
1.0.0 release: `manifest.pdxsig`'s dual ML-DSA-65 signature blocks
remain `PENDING` — the ml_dsa_65_sign intrinsic, the svc.signing-bot
broker, and the pkgs.paideia-os host all land at paideia-as v0.33
("crypto"), still ahead of this repo's HEAD (see manifest.pdxsig's own
substrate-posture note). `pkg install --strict` refuses this package
until a release runner replaces both PENDING blocks — same posture
every other satellite in this org ships pre-v0.33.

- **#6 (M4-002 close-on-WM-quit smoke)** —
  `tests/pdxclock_wm_quit_smoke.pdx` (`PdxclockWmQuitSmoke::run`)
  feeds a canned 24-byte WM_QUIT-shaped `PdxWindowRecord`
  (`kind == WINDOW_KIND_CLOSING`) through a mocked `ipc_recv`/
  `ipc_send` pair into the real `Close::close_poll`. `close_poll`'s
  own `sys_exit(0)` is a bare SYSCALL instruction, not a callable
  symbol, so it cannot be intercepted by an in-process mock counter
  at this toolchain's current (pre-v0.33) capability; the fixture's
  assertion is therefore the process's own externally-observable exit
  status (0 on the expected CLOSING/sys_exit path) rather than an
  in-process counter — see the file's own header for the full
  rationale, including why the fixture targets `close_poll` rather
  than the issue text's `cls_run_tick` (no such symbol exists in this
  repo; `cls_` is close.pdx's label prefix, not a function name).
- **#7 (M5-001 signed 1.0.0 release)** — `manifest.pdxsig` bumped to
  package version 1.0.0 / git tag `v1.0.0-src`, content-hash entries
  extended to cover `tests/pdxclock_wm_quit_smoke.pdx`, both signature
  blocks left `PENDING` per the substrate-gate note above. README
  Status section updated to reflect all five R102 milestones landed.

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

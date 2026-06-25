# GMOD Starfall Releases

Release folder for Garry's Mod Starfall chips, shared helper libraries, and
reference examples.

The files are meant to live under:

```text
garrysmod/data/starfall/GMOD-Starfall-Releases/
```

Keep the folder layout intact. Most chips use relative `--@include` paths into
`allylib/`, so moving individual files without their dependencies will break
loads in-game.

## Layout

- `allylib/`: shared libraries for wire I/O, networking, HUD drawing, ACF
  ballistics, projectile simulation, animation helpers, and general utilities.
- `examples/`: small reference chips and demos.
- `misc/`: standalone tools and utilities.
- `defunct/`: archived scripts kept for reference.
- root `.txt` files: main released chips.

## Main Chips

- `2024_physical_gun_controller.txt`: physical gun/aimer controller.
- `ally_mobility_hours.txt`: ACF vehicle mobility controller.
- `lowlag_multi_function_displays.txt`: multi-function display system.
- `lowlag_radar_track.txt`: low-lag radar tracking chip.
- `merydians_camera_chip.txt`: seat-driver camera and aim output chip.
- `merydians_public_gun_controller.txt`: public ballistic gun controller.
- `merydians_sensor_suite.txt`: sensor suite and display companion.

## Core Libraries

- `allylib/simple_wire_helper.txt`: direct-global wire helper layered over the
  networking backend. Use this when a chip wants `_G.Base`, `_G.HitPos`, and
  other helper-managed input/output globals.
- `allylib/starfall_wire_networking_interface.txt`: lower-level wire, NetVar,
  dirty-send, connect-send, and output wrapper backend.
- `allylib/general_algorithms.txt`: shared math, sanitizers, protected net send
  helpers, gun helpers, and seat/baseplate driver resolution helpers. This file
  is marked `--@shared` because several shared chips consume its globals on both
  realms.
- `allylib/helpful_hud_functions.txt`: HUD draw wrappers, colors, font helpers,
  render-target helpers, and scaling utilities.
- `allylib/merys_integrated_ballistics_computer.txt`: ACF-style static
  ballistic solve helper used by the public gun controller.

## Shared Helper Conventions

- Register wire ports once with `updateInputs(...)` and `updateOutputs(...)`.
- Prefer `Inputs.<Name>:get()`, `Outputs.<Name>:update(value)`, and
  `inputClk(name, fn)` for explicit code paths.
- Use `simple_wire_helper.txt` direct globals only when that style keeps the chip
  simpler; do not redeclare those globals as `local`.
- `simple_wire_helper.txt` auto-networks direct input globals by default. For
  server-only chips, or for shared chips that send their own summarized/client
  payloads, call `unnetworkInputs(true)` right after `updateInputs(...)` and
  explicitly re-enable only the inputs the client must read.
- Use unique hook, timer, and net-message names per chip instance. `entIndex()`
  is the usual suffix for pasted or duplicated systems.
- For live pose or aim streams, cap send cadence and prefer the helper
  `tryNetSend(...)` path with payload estimates.
- Chair, Seat, and Base inputs may be wired either to a real seat/pod or to an
  ACF baseplate. Use `resolveSeatFrom(ent, chip())` and
  `seatActiveDriver(seat, chip())` from `general_algorithms.txt` to handle both.

## Usage

1. Copy or clone this folder to `garrysmod/data/starfall/`.
2. Load the chip you want in-game.
3. Keep every `--@include` dependency in the expected relative path.
4. For multi-chip systems, load and wire the matching companion chips together.
5. When updating helper libraries, reload dependent chips so both client and
   server realms run the same helper version.

## Notes

- `examples/` is for reference. Not every example is production style.
- `defunct/` is archived intentionally; do not treat it as the current version.
- Helper-managed outputs suppress unchanged values internally, so chip-level
  duplicate suppression is only needed for genuinely expensive calculations.
- If a shared chip consumes a global helper function, the helper file must be
  available in the correct realm through Starfall metadata such as `--@shared`.

## Links

- GitHub: <https://github.com/Ally-for-Hire>
- Legacy Drive: <https://drive.google.com/drive/folders/1arb3QgrZkKfF06vdEVqD4samK23IfJrT?dmr=1&ec=wgc-drive-globalnav-goto>
- YouTube: <https://www.youtube.com/@allyforhire>
- Steam: <https://steamcommunity.com/id/alliedmerydian/>
- Discord: `@allyfh`

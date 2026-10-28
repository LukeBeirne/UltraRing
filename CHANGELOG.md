# Changelog

## Unreleased

- **Kit features with Elden Ring.** The host DLLs now come from
  [LukeBeirne/Minecraft-Ring](https://github.com/LukeBeirne/Minecraft-Ring) at `858be28`, which implements the kit's
  optional extensions for Elden Ring (`ultrakill.cpp`). They switch on only while the ULTRAKILL guest is attached:
  - host-owned health: the Tarnished's HP is V1's bar; dash/hurt i-frames and punch parries cancel Elden Ring hits,
    ULTRAKILL heals heal the Tarnished, a lethal hit kills V1 first and then the Tarnished;
  - weapon progression: one weapon at the start, one more per boss defeated in the save (global boss flags
    9100-9299), order fixed per character;
  - stats: Dexterity drives attack speed, max FP alt-fire recharge, max stamina dash recharge;
  - stat damage: V1's damage scales with the best weapon's attack rating;
  - menu hand-off: Site of Grace, talking and item pop-ups give Elden Ring the controls until they close.
  - Settings in `runtime\er-bridge.ini` (`[ULTRAKILL]`).
- `tools/Build.ps1` no longer stops when the pinned Minecraft Ring commit is missing from an existing checkout
  (Windows PowerShell turned git's stderr into an error before the fetch could run).

- **Elden Ring 1.17.2** (`eldenring.exe` 2.7.2.0) is supported. `tools/Build.ps1` now builds the host DLLs from
  [LukeBeirne/Minecraft-Ring](https://github.com/LukeBeirne/Minecraft-Ring) at `8923324` (0.3.0 plus a 1.17.2 address
  table; 1.17.1 still works) and fetches that commit into an existing `external/minecraft-ring` checkout if needed.
- **Kit submodule** now comes from [LukeBeirne/ultrakill-crossover-bridge](https://github.com/LukeBeirne/ultrakill-crossover-bridge)
  `main` (upstream kit `main` as of `663ed6c` plus fixes): run `git submodule sync` and
  `git submodule update --init` in an existing clone.
  - Upstream kit changes since 0.1.2 are included. Their host features (host-owned health, stats, weapon progression,
    native prompts, stat damage) stay off because the Elden Ring DLL does not advertise them. Changes that do apply with
    Elden Ring: cheats are off while bridged (`AllowCheats`), `WorldAlpha = Matte` by default (one extra world render;
    `MaxRgb` is the old single pass), void rescue after long falls (`VoidRescue`, `VoidRescueDepth`), holding the
    interact key repeats it (`HoldRepeat`), and the kit's build keeps the staged guest's config and logs.
  - Rocket rides: terrain is sampled ahead along the rocket's path (rides zero V1's own velocity), so V1 no longer flies
    through walls that were never sampled.
  - F8/F9/F10 also work when another program (overlay, voice chat, peripheral software) has the key as a global hotkey.
  - F9 diagnostics are visible in Elden Ring (they were drawn on ULTRAKILL's invisible window) and show V1's speed.
  - F8 from Elden Ring no longer bounces straight back to Elden Ring.

## 0.3.0

Split into two repositories. The ULTRAKILL side now lives in the reusable
[ULTRAKILL Crossover Bridge](https://github.com/Themetralla3000/ultrakill-crossover-bridge) kit (v0.1.0), consumed here as the git submodule `bridge/`.
UltraRing is now the Elden Ring host integration: Minecraft Ring's host DLLs, Elden Ring install/launch/restore, docs.

- **Removed** `src/UltraRing.Link`, `src/UltraRing.Ultrakill`, `tools/UltraRing.FakeHost`, `tests/UltraRing.Link.Tests` and `UltraRing.sln`
  (now `UltrakillBridge.Protocol`, `UltrakillBridge.Guest`, the kit's fake host and tests).
- **Build.ps1** builds the host DLLs, then calls `bridge\scripts\Build.ps1`; the guest is staged at `bridge\dist\guest`
  (was `runtime\ultrakill-bepinex`). Needs `git clone --recursive` / `git submodule update --init`.
- **Install/Restore/Launch/Stop** delegate the ULTRAKILL part to the kit's `Install-Guest`, `Uninstall-Guest`, `Launch-Guest`, `Stop-Guest`;
  `-FakeHost` uses the kit's `Run-FakeHost.ps1`. An `installation.json` from 0.2.0 keeps working.
- **Renamed:** plugin GUID `dev.ukbridge.guest`, config `dev.ukbridge.guest.cfg` (Build.ps1 copies your old config over once),
  `ULTRARING_WINDOW_MODE` -> `UKBRIDGE_WINDOW_MODE`, bridge dir variable `UKBRIDGE_DIR` (`ERMC_DIR` is still read).
- Research notes in `docs/research/` are kept as history; current docs are in the kit.

## 0.2.0 — 2026-10-08

First version played in Elden Ring (only lightly tested so far).

- **Camera and stand-in:** Elden Ring renders from V1's eyes; the invisible Tarnished follows V1's feet and facing and takes enemy aggro.
- **Terrain:** host raycasts rebuilt as ULTRAKILL colliders — floors, slopes, stairs, walls from knee/chest/head probes, ceilings;
  look-ahead sampling along V1's velocity; per-zone terrain cache on disk; F10 debug view inside Elden Ring.
- **Combat:** every ULTRAKILL weapon, punch and slam hits Elden Ring enemies through invisible proxies; damage converted to a share of
  max HP; style, freshness and blood healing; coins; whiplash pulls V1 to enemies; timed punch parries host attacks.
- **Damage in and shared life:** stand-in HP loss becomes V1 damage (dash i-frames dodge it); deaths shared both ways; recall to the
  Site of Grace after respawns.
- **Interactions:** Elden Ring's prompts on V1's HUD, performed with V.
- **Compositing:** V1's viewmodel, effects and HUD captured with dedicated cameras and drawn into Elden Ring's frame.
- **Window and focus:** borderless, almost transparent input window owned by Elden Ring; F8 control switch; Elden Ring forced to
  borderless mode; `Focus.bat` and `Stop.bat` recovery helpers.
- **Tooling:** `Build.ps1`, `Install.ps1`, `Launch.ps1` (incl. `-FakeHost`), `Restore.ps1`; a WinForms fake host for development.

## 0.1.0 — 2026-10-07

Initial scaffold: protocol mirror of Minecraft Ring's `bridge_protocol.h`, guest plugin skeleton, layout tests, research notes.

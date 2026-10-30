# MKS-80 Max for Live v46 — Release Notes

**Suggested GitHub release tag:** `v46`  
**Suggested release title:** `MKS-80 Max for Live v46 — Automatic APR reception, validation and faster preset transfer`

This release brings together all improvements since v36. The editor now receives MKS-80 and KnobKraft preset dumps **automatically**, validates them before applying changes, prevents unwanted SysEx feedback, and organizes the Upper/Lower parameters more usefully for Live automation and external controllers.

## Highlights

- **No more receive buttons.** The device continuously listens for complete MKS-80-compatible SysEx dumps.
- **Automatic source-format handling.** Recognizes **four APRs** from KnobKraft-style transfers or **PGR + four APRs** from the MKS-80.
- **No IPR flood on import.** Incoming dumps populate Live's parameter controls with outgoing IPR suppressed. A PGR-prefixed hardware dump is not echoed back to the synth.
- **Efficient preset forwarding.** If SysEx output is enabled, a four-APR import sends four complete APR blocks (158 bytes total), now spaced **100 ms apart** instead of 1,000 ms.
- **Validation and diagnostics.** Invalid, incomplete, duplicate or incorrectly formatted dumps are rejected without partially loading their parameters. A compact visible status display reports success/errors; hover for details in **Live's Info View**. The Max Console also records diagnostics.
- **Better parameters for controllers.** Upper/Lower names use explicit `Upper`/`Lower` suffixes (short names `U`/`L`), and v46 Recall Order places each Upper parameter immediately before its Lower partner.
- **Less legacy circuitry.** Removed the obsolete second validator and old manual-receive circuits; one embedded V8 receiver remains.

## What happens when receiving a preset?

| Incoming transfer | Live device | MIDI output to MKS-80 |
|---|---|---|
| Four valid APRs | Updates Patch/Tone controls automatically | Four APRs if `Enable Sysex Out` is on; none otherwise |
| Valid PGR + four APRs | Updates controls and program display automatically | **No echo** |
| Individual IPR | Ignored by the preset receiver | No automatic import/forward |
| Invalid or incomplete dump | Reports error; does not apply partial preset | None from that failed import |

Manual edits continue to send **individual IPR messages** when output is on.

## Upgrade notes

- **Requires a Max 9-compatible Max for Live runtime** because the embedded receiver uses `v8.codebox`. The v46 file is saved with Max 9.1.5.
- **Deleted obsolete automation targets**: `Receive Sysex from MKS80`, `Receive Sysex from Knobkraft`, `clear`, `sendsx2Dev`. Check existing Live Sets using those old parameters.
- Surviving parameter types, ranges and internal identifiers are preserved, but **v46 changes Recall Order (0–130)**; custom controller banks or setups depending on earlier parameter order should be reviewed.
- `Enable Sysex Out` defaults to **off** on device load. Enable it to send manual edits or four-APR imports to the hardware.
- A **four-APR-only hardware dump is indistinguishable from a KnobKraft dump** unless source ports are separated. Such a dump may be forwarded when output is on. Five-message hardware transfers do not echo.
- A just-forwarded identical four-APR dump is ignored briefly (around 5.5 seconds) as an echo safeguard.
- Program Change alone still cannot automatically read back the selected MKS-80 preset. The proposed bulk-RQF workaround is tracked in [issue #5](https://github.com/markusschloesser/MKS-80_M4L/issues/5).

## Hardware testing

The current development versions were tested with a real MKS-80. The v44 automatic-reception workflow passed tests for KnobKraft imports with output off/on, PGR-prefixed hardware imports with no MIDI echo, and normal manual IPR editing. The v41 APR forwarding was compared byte-for-byte against a KnobKraft preset (126/126 payload bytes matched). The user subsequently confirmed v45 working well in Live. The v46 parameter ordering was structurally checked, but a separate v46 runtime test has not been reported here. These tests do not establish compatibility with every MIDI interface, Live/Max version or MKS-80 firmware revision.

## Changes by version

- **v46** — Upper/Lower pairs together, Upper first, in parameter Recall Order (also improves external controller order).
- **v45** — 100 ms APR pacing, remove obsolete receiver/validator code, rename Upper/Lower parameter labels.
- **v44** — Always-on validated APR/PGR import with automatic routing and echo prevention.
- **v43** — Place status/error display inside visible device UI.
- **v42** — V8 validation and detailed errors in Max and Live Info View.
- **v41** — KnobKraft APR forwarding replaces IPR burst.
- **v40** — Block IPR feedback when receiving from MKS-80.
- **v39** — Complete shared Upper/Lower Key Mode, Split Point and Balance synchronization; suppress duplicate IPR.
- **v38** — KeyMode double fix.
- **v37** — Fix Keyboard Split Point receive conversion.
- **v36** — APR-based state restoration (earlier baseline).

Full details: [CHANGELOG.md](CHANGELOG.md).

## Download / installation

Attach **`MKS-80 v46 - Upper Lower Recall Order by gpt.amxd`** to this GitHub release, or add it to the repository. Drag the `.amxd` onto a MIDI track in Live, route SysEx-capable MIDI input/output, and use **Enable Sysex Out** only when sending changes to the synth.

Questions, bug reports and ideas: [GitHub Issues](https://github.com/markusschloesser/MKS-80_M4L/issues).

Thanks to **Source Audio** and the Cycling '74 community for the earlier advice and feedback that helped this project along.

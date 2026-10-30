# Changelog — MKS-80 Max for Live

Version numbers refer to the `.amxd` filename, not semantic versioning. This documents the changes since v36, including the final v46 hardware-tested behavior and the intermediate implementation milestones. The v37 and v38 release descriptions were clarified by the maintainer.

## v46 — Upper/Lower parameter recall order

- Assigned **unique, consecutive Recall Order values (0–130)** to all **131 remaining Live parameters**.
- Grouped all **63 Upper/Lower parameter pairs** together, with **Upper first, then Lower**; corrected separated Key Mode, Keyboard Split Point and Balance pairs.
- This order also determines the default parameter-bank order exposed to external control surfaces, where not overridden by a custom mapping.
- **No changes** to MIDI logic, parameters' names, types, ranges, internal `varname`s, control layout or patch cords.

## v45 — Faster APR and code cleanup

- Changed the four-APR sender from **1,000 ms to 100 ms** spacing (`metro 100`), while keeping the separate startup delay.
- Removed the unused, legacy V8 validator and old manual receive circuitry, retaining the active automatic V8 receiver.
- Removed obsolete manual receive/utility parameters: **Receive Sysex from MKS80**, **Receive Sysex from Knobkraft**, **clear** and **sendsx2Dev**.
- Standardized **126 Upper/Lower parameter names** to `Name Upper` / `Name Lower` and short names to `Name U` / `Name L`; removed the `[1]` suffix. Existing surviving internal parameter identifiers were preserved.
- Preserved the four parameter subpatchers and existing APR/IPR routing.

## v44 — Automatic APR/PGR reception

- Replaced both manually enabled receivers with an **always-on V8-based** SysEx receiver.
- Automatically accepts **four APR messages** (KnobKraft-style edit-buffer dump) or **PGR + four APR messages** (MKS-80 hardware dump).
- Identifies Upper/Lower Patch/Tone blocks by **header**, independent of arrival order.
- Ignores incoming IPR for the purpose of full-preset import.
- Applies parameter values only after all required blocks validate; suppresses individual IPR output during import.
- **APR-only import:** if `Enable Sysex Out` is on, sends the four reconstructed APR blocks to the MKS-80.
- **PGR-prefixed hardware import:** updates program display and controls without sending MIDI back to the synth.
- Added a short duplicate/echo safeguard for just-forwarded APR dumps.
- Retained the improved status/error UI and Live Info View annotations.

## v43 — Receive-status display layout

- Repositioned the SysEx status/error indicator **inside the visible device area** and reduced its dimensions.
- Refined adjacent receive-control sizing, alignment and presentation colors.
- No functional change to the then-existing MIDI receive/validation paths.

## v42 — SysEx validator and user-facing errors

- Added embedded **V8 SysEx validation** for MKS-80 **PGR + four APR** and KnobKraft **four APR** batches.
- Validated framing, Roland/MKS-80 headers, block roles, lengths, legal data bytes, duplicates/missing blocks and channel consistency.
- Prevented invalid or incomplete batches from updating the parameter controls or triggering APR forwarding.
- Added persistent reception status/errors in the device and detailed error annotations for **Live's hover-driven Info View**, plus Max Console diagnostics.
- Initially retained separate manual receive controls; these were superseded in v44 and removed in v45.

## v41 — KnobKraft import: APR rather than IPR

- Added IPR suppression while KnobKraft's incoming APR data updates Live parameter controls.
- Triggered the existing **four-message APR sender** after a successful KnobKraft import when output was enabled.
- Prevented the earlier approximately **1,050-byte flood of individual IPR messages**; outgoing APR blocks total **158 bytes** (23 + 23 + 56 + 56).
- Preserved normal one-parameter IPR editing and MKS-80 hardware import behavior.
- Initial APR spacing remained 1,000 ms; reduced to 100 ms in v45.

## v40 — Suppress feedback on MKS-80 receive

- Fixed the timing of the MIDI output gate: it had reopened before captured MKS-80 APR data was applied to the Live controls.
- Added an import lock (`ipr_allowed`) around the actual parameter update, re-enabling normal IPR editing after completion.
- Eliminated unwanted outgoing IPR during MKS-80-originated preset imports.

## v39 — Shared Patch settings synchronization

- Completed bidirectional synchronization of **Key Mode**, **Keyboard Split Point** and **U/L Balance** between Upper and Lower controls.
- Updated both Upper and Lower Patch APR buffers from the shared values.
- Removed duplicate Lower-side IPR transmission for these shared parameters; ordinary edits produce one IPR message.
- Preserved the original Live controls and their automation identities.

## v38 — KeyMode double fix

- Fixed the duplicated **Upper/Lower Key Mode** control behavior.

## v37 — Split Point receive fix

- Fixed the incoming **Keyboard Split Point** receive conversion (MKS-80 MIDI note-number offset).

## v36 — Full-preset recall correction (previous release)

- Changed full-preset restoration from **IPR (`F0 41 36`) to APR (`F0 41 35`)** to fix recall after reopening a Live Set.

---

## Earlier history (from the original README)

- **v35:** KnobKraft edit-buffer import, Upper/Lower Balance fixes, correct program-number display for MKS-80 imports.
- **v32:** Bidirectional editor operation.
- **v14 / 1.2:** VCO-2 Range visualization and F ENV Polarity fixes.
- **v10 / 1.1:** Global Volume control, cosmetic changes, fewer initial parameter values.
- **v08 / 1.0:** Initial release.

## Compatibility notes for upgrading from v36

- **MIDI reception is now automatic.** No receive-arm toggles remain.
- **`Enable Sysex Out` is the transmission control**, not the receive enable control. It starts off when the device loads. Five-message hardware dumps update Live without feedback; four-APR imports may be forwarded when output is enabled.
- **Some old automation targets were removed in v45**, specifically the obsolete manual receive and utility controls. Surviving parameter identities were kept, but **recall order changed in v46**, which may change controller bank placement and restoration sequence.
- **Max 9-compatible V8 runtime required.** The new receiver is an embedded `v8.codebox`; there are no external JavaScript dependencies.
- **Source ambiguity:** four APRs without PGR cannot identify KnobKraft versus hardware by themselves. If a hardware dump contains only four APRs, it can be forwarded like a KnobKraft preset when output is enabled.
- **Edit-buffer requests on Program Change are not implemented.** See [issue #5](https://github.com/markusschloesser/MKS-80_M4L/issues/5).

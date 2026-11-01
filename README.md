# Maxforlive device for the Roland MKS-80
Maxforlive device for controlling the Roland MKS-80 from Ableton / Max for Live
<img width="1334" height="145" alt="image" src="https://github.com/user-attachments/assets/c4b785b0-abbc-4d5e-9182-846b84ae33c5" />
<img width="1105" height="131" alt="image" src="https://github.com/user-attachments/assets/feca92ca-3c6a-4112-8168-c09ef4bc36e3" />
<img width="949" height="350" alt="image" src="https://github.com/user-attachments/assets/9b7e2821-8483-4756-90ad-1f6d4269450c" />



# MKS-80 Max for Live

A Max for Live device for editing and automating the **Roland MKS-80** from **Ableton Live**.

The MKS-80 primarily uses MIDI SysEx for editing. This device exposes (nearly, Tune Request doesn't work due to a bug in Ableton Live) all of its Patch and Tone parameters as Live controls, so you can automate filter cutoff, envelopes, LFOs, oscillator parameters and more, and use hardware controllers instead of relying on the MKS-80 front panel.

**Latest device version: v50.** The current version receives compatible preset dumps automatically: you no longer have to enable separate MKS-80 or KnobKraft receive buttons.
## New interface

Four MGraphics/V8UI ADSR previews are embedded in the existing 170-pixel-high presentation UI: Upper Filter, Upper Amp, Lower Filter and Lower Amp.

**Click `EDIT` in the top-right corner of any preview** to open the single floating ADSR editor. The pop-up contains four larger 395 x 170 interactive graphs, laid out as two rows (Upper / Lower) and two columns (Filter / Amp). Filter envelopes are teal; Amp envelopes are amber.

### Dragging

- **A peak:** drag horizontally to change Attack.
- **D end:** drag horizontally to change Decay, and vertically to change Sustain.
- **S plateau:** drag vertically to change Sustain.
- **R endpoint:** drag horizontally to change Release.

The horizontal axis is a *normalized visual depiction* of the MKS-80's 0–100 parameter values, **not a calibrated time in milliseconds**. Sustain height represents 0–100. A fixed segment depicts key hold: it is not an additional programmable envelope phase.

> **A note about real-time MIDI:** SysEx editing works, but the MKS-80 can stumble when a lot of MIDI arrives while it is playing notes. Fast parameter sweeps are possible; don't assume that heavy simultaneous note and SysEx traffic will always be perfectly timed.

## Requirements

- Roland MKS-80, connected to a MIDI interface with SysEx-capable input and output.
- Ableton Live with Max for Live and SysEx support (developed and tested with Live 12).
- A **Max 9-compatible** Max for Live runtime: the automatic receiver uses an embedded `v8.codebox` (the v47 device was saved with Max 9.1.5).
- Optional: [KnobKraft-orm](https://github.com/christofmuc/KnobKraft-orm) for managing and sending MKS-80 patches.

## Installation and MIDI routing

1. Download the newest `.amxd` (currently **`MKS-80 v47`**) from this repository or from the release assets, and drag it onto a MIDI track in Live.
2. Configure the track and MIDI interface so the device can receive SysEx from the MKS-80 or KnobKraft. For incoming MIDI from the hardware, arm the track or otherwise enable MIDI monitoring, as appropriate for your Live setup.
3. Configure MIDI output so that outgoing SysEx reaches the MKS-80.
4. Turn on **Enable Sysex Out** when you want edits or imported KnobKraft presets to be sent to the hardware. **It starts ON when the device loads**.

There is **no need to enable reception**: the new validator listens continuously for complete, compatible APR dumps.

## How to use

### Edit and automate parameters

Change a control in Live to edit the MKS-80. With **Enable Sysex Out** on, a manual adjustment sends a single Roland **IPR** (`F0 41 36 ...`) parameter message. The controls are exposed for Live automation and hardware controller mapping.

Names ending in **Upper** or **Lower** distinguish the two synthesizer sections; short names use **U** or **L**. Shared MKS-80 settings—**Key Mode**, **Keyboard Split Point** and **U/L Balance**—are synchronized between their Upper and Lower controls. The parameter/recall order places Upper immediately before Lower for each pair, which also gives a more useful default order on external control surfaces.

A few controls use abbreviated labels. **F** generally means filter (for example, `F ENV Src`), and **A** means amp/VCA. Hover over controls for explanatory annotations in Live's Info View; some controls have additional popup hints.

### Receive a preset from the MKS-80

Select a patch **on the MKS-80 itself** (with the synthesizer configured to transmit its SysEx data) so that it sends its program information and APR data to Live. The receiver recognizes **PGR + four APR messages** (five messages in total), validates the transfer and updates the Live controls and program display automatically.

**Nothing is sent back to the MKS-80** as a result of a recognized five-message hardware dump—even when **Enable Sysex Out** is on. This prevents the old flood of unnecessary IPR feedback messages.

A standard MIDI Program Change sent *to* the MKS-80 does not, by itself, request a complete edit-buffer response. Automatic readback after Program Change, possibly via an RQF bulk-memory request, is a [separate feature request](https://github.com/markusschloesser/MKS-80_M4L/issues/5), not part of v46.

### Receive a preset from KnobKraft

Send an MKS-80 edit-buffer preset from [KnobKraft-orm](https://github.com/christofmuc/KnobKraft-orm) to the device's MIDI input. There is **no import button** to click.

The device recognizes the **four APR blocks** (Upper Patch, Lower Patch, Upper Tone and Lower Tone), validates them, then updates all Live controls without generating individual IPR messages. If **Enable Sysex Out** is ON, it forwards the complete preset to the MKS-80 as **four APR messages** (`F0 41 35 ...`), each separated by **100 ms**. This is **158 transmitted bytes** in total. If output is OFF, the controls update but nothing is forwarded.

An APR-only transfer does not include the selected program number, so the device cannot infer the program number from KnobKraft's edit-buffer data.

**Source-detection limitation:** A four-APR transfer alone has no reliable sender identity. v46 interprets it as a KnobKraft-style transfer. An MKS-80 hardware dump without a PGR header would be treated the same way and could be forwarded to the synth if output is enabled. For standard five-message MKS-80 transfers, there is no such echo.

### SysEx validation and error messages

The receiver runs continuously using an embedded V8 validator. It:

- Accepts the four distinct Patch/Tone APR blocks (with or without a preceding PGR).
- Identifies blocks by **SysEx header**, so arrival order does not matter.
- Ignores incoming individual **IPR** parameter messages when collecting a preset.
- Checks SysEx framing, Roland/MKS-80 message headers, message lengths, data bytes, duplicates, missing blocks and consistent MIDI channel information.
- Rejects malformed or incomplete transfers **without partially applying the preset**.

The device shows the last reception status or error **inside the visible device area**. Hover over the status display to read more detail in **Ableton Live's Info View** (show Info View with `?` in Live). Diagnostic output is also available in the Max Console.

A short echo safeguard suppresses an identical just-forwarded four-APR dump for approximately 5.5 seconds; immediately resending the same exact preset can therefore be ignored during that window.

### Save, restore and automation

Live can store the device's parameter state in a Set. When restored, the device uses complete **APR** messages (`F0 41 35 ...`) instead of individual IPR messages to restore the synth state. The sender uses 100 ms between the four APR blocks, with a separate startup delay.

Automation and external-control ordering follow the parameter's **Recall Order**. Since v46, all Upper/Lower pairs are adjacent, Upper first. The order has been deliberately reorganized; check any saved controller assignments or Live Sets that depend on previous parameter ordering.

## Versions and changes

- **v50** — now with graphical ADSR Envelope editors for all 4 ENVs and an additional pop-up window for bigger and easier editing 
- **v47** — Design changes
- **v46** — Reordered all 131 remaining Live parameters (Upper then Lower, adjacent pairs) for recall and external-controller banks.
- **v45** — Reduced APR spacing to 100 ms; removed obsolete receive/validator code and manual receive controls; standardized Upper/Lower parameter names and short names.
- **v44** — Automatic always-on APR/PGR reception, IPR filtering, validated import and conditional KnobKraft APR forwarding.
- **v43** — Improved the placement and layout of the visible receive status/error display.
- **v42** — Added V8 SysEx validation, error reporting and Live Info View annotations.
- **v41** — Blocked unwanted IPR during KnobKraft imports and sent four full APR messages instead.
- **v40** — Blocked unwanted outgoing IPR while receiving a preset from the MKS-80.
- **v39** — Completed Upper/Lower Key Mode, Split Point and Balance synchronization and removed duplicate Lower-side IPR for shared parameters.
- **v38** — KeyMode double fix for duplicated Upper/Lower controls.
- **v37** — Fixed Keyboard Split Point receive conversion.
- **v36** — Fixed Live Set state recall by using APR (`F0 41 35`) rather than IPR (`F0 41 36`) for full-preset sending.

See [CHANGELOG.md](CHANGELOG.md) for the full version history and [v46 release notes](RELEASE_NOTES_v46.md) for the changes since v36.

## Known limitations and future plans

- A standard Program Change cannot request a complete edit-buffer dump. See [issue #5](https://github.com/markusschloesser/MKS-80_M4L/issues/5) for the proposed RQF/bank-dump workaround.
- APR-only reception cannot identify the physical sender without a separate MIDI-port or source distinction.
- Excessive real-time SysEx can affect the MKS-80's response while playing.

Found something not working? Please [open an issue](https://github.com/markusschloesser/MKS-80_M4L/issues).

## Earlier history

The project started as a Max for Live editor, with the following milestones:

- **1.0 / internal v08** — Initial release.
- **1.1 / internal v10** — Added global Volume control; cosmetic improvements and removal of most initial values.
- **1.2 / v14** — Fixed VCO-2 Range display and F ENV Polarity assignment.
- **v32** — Added bidirectional operation.
- **v35** — Added KnobKraft edit-buffer import; Upper/Lower Balance fix; correct original preset number for MKS-80 imports.

Huge thanks to **Source Audio** on the [Cycling '74 forum](https://cycling74.com/forums/roland-mks-80-m4l-device-questions-critique-suggestion) for the help and patience with the device!
I heavily relied on ChatGPT for the last couple of versions.

## Support the music

If you like the device, you can also support the project by listening to my music:

- [Personality Development](https://distrokid.com/hyperfollow/markusschloesser/personality-development)
- [Tag und Nacht](https://distrokid.com/hyperfollow/markusschloesser/tag-und-nacht)

## License

[LGPL-2.1](LICENSE).

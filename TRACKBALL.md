# Trackball tuning

## Profile applied

Normal tracking stays at 1000 CPI, divider 1, orientation 180 degrees.

- Pointer acceleration: sigmoid (algorithm 2), sensitivity 3. Small single-count movements are preserved by the driver; fast movement gains more travel. This is a starting point, not a hardware-tested optimum.
- Precision: hold the physical Z key while the MOUSE layer is active to enter layer 5, at 400 CPI, divider 1. Release to return. To access reliably without moving first, hold the existing mouse-layer thumb key, then Z. Precision has explicit mouse bindings so clicks remain available after the automouse timeout.
- Precision still passes through the driver's acceleration function. It is not a completely unaccelerated mode, but its lower CPI reduces input speed into that curve.
- Polling: hardware 125 Hz instead of software 125 Hz. The software implementation caches one report and combines it with the next; an isolated final report can remain pending. Hardware reporting avoids that software pairing path.
- Scrolling: hold the existing Tab/SCROLL thumb key and roll. Tick stays 144; scroll acceleration is enabled at sensitivity 3. Existing horizontal/vertical directions are unchanged.
- Automouse: activation threshold 3 instead of 0; timeout 900 ms instead of 700. Threshold is a per-report Manhattan movement test, not an accumulated threshold or pointer dead zone. Very slow pointing may not activate MOUSE; use the manual mouse thumb key when needed.
- Game layer is unchanged. Global pointer acceleration also applies during games; there is no per-layer acceleration toggle in this driver.

## Available options in the installed driver

Inspected driver revision: 77a61804cf0d51a495a22e60d1a3115568a441df.

- Normal and snipe CPI: 200–3200, in 200-CPI steps.
- Separate normal/snipe integer divisors: 1–100. Avoid increasing these for precision: the implementation truncates each report without preserving fractional remainders.
- Pointer acceleration: disabled, quadratic, or sigmoid; sensitivity advertised as 1–100. Prefer sigmoid. The quadratic implementation uses `22 - 2 * sensitivity` as its divisor: sensitivity 11 divides by zero and larger values produce a negative divisor. Do not treat its full advertised range as safe.
- Scroll ticks, scroll acceleration, sensitivity 1–10, and independent axis inversion.
- Layer-selected scroll and snipe modes. Selection uses the highest active layer, with scroll checked before snipe.
- Automouse target layer, activation threshold, and inactivity timeout.
- 250 Hz, hardware 125 Hz, or software-paired 125 Hz reporting.
- Rotation, axis inversion, surface-tracking SMART_ALGORITHM, rest sampling/downshift timers, forced-awake mode and power management. SMART_ALGORITHM improves surface coverage; it is not pointer acceleration.
- Ball-action layers can turn directional ball motion into four key behaviors, e.g. arrows. This is optional and not enabled in this profile.

Not supplied as configurable features by this driver: a strict unaccelerated precision mode, a capped/custom acceleration curve, drag lock, inertia, or a configurable scroll axis lock. These need separate behaviors or driver/input-processing work. Current upstream ZMK has input processors, but this repository pins ZMK v0.3; do not paste current upstream processor snippets into it without checking compatibility.

## Practical validation after flashing

Build success verifies configuration and compilation, not feel or runtime sensor behavior. Flash both matching halves because the keymap changed. Do not flash settings_reset for routine tuning.

1. Move very slowly toward a small target; check for missing movement or unexpected jumps.
2. Flick across the screen, then settle on the target. If overshoot is excessive, lower sigmoid sensitivity to 2 before changing CPI.
3. Hold the mouse thumb key and physical Z. Try text selection and dragging a small window edge; verify left/right click after holding still for more than a second.
4. Release precision and verify normal movement returns.
5. Hold Tab to scroll: compare slow single-step movement with fast rolls, then test horizontal scroll. If fast scrolling is excessive, lower scroll acceleration sensitivity to 2.
6. Type immediately after pointing and watch for accidental mouse bindings. Adjust timeout/threshold independently; increasing the threshold can make automouse harder to activate at low speeds.
7. Test game use separately because acceleration is global.

Keep host-side pointer settings unchanged during these tests so firmware changes can be judged independently. If tracking is physically sticky before movement starts, firmware cannot establish whether ball/bearing maintenance is needed; inspect the mechanism rather than compensating with stronger acceleration.

## Files

- Sensor tuning: config/boards/shields/keyball_nano/keyball44_right.conf
- Mode layers: config/boards/shields/keyball_nano/keyball44_right.overlay
- Precision binding/layer: config/keyball44.keymap
- Build: ./scripts/build-local.sh

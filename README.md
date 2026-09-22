# Coupling Cone Calculator

An interactive, self-contained calculator for sizing a water coupling
cone between a focused bowl ultrasound transducer and a target — cone
height, opening diameter, and reachable focal depth — calibrated against
k-Wave simulation of a 35 mm / 24.5 mm-ROC bowl transducer at 0.5 and
1.45 MHz.

Live copy: [Coupling Cone Calculator](https://claude.ai/artifact/K9TJRAH7roKvFh7YihWprz)

## Running it

Everything is in one file. No build, no server, no dependencies:

```
open cone_planner.html      # macOS
xdg-open cone_planner.html  # Linux
```

or just double-click it / drag it into a browser tab.

`gallery/` holds the real k-Wave field-map PNGs the calculator's
simulation viewer displays — keep it next to `cone_planner.html` (the
page references them with the relative path `gallery/field_*.png`).

## What it does

- **Forward calculator** — enter aperture diameter, radius of curvature,
  and drive frequency; get the minimum safe cone height (shorter cones
  collapse the coherent focus), the matched opening diameter at that
  height, and the deepest reachable tissue depth, plus pressure- and
  depth-vs-height charts.
- **Target a specific depth** — enter a desired depth (measured back
  from the transducer's natural focus point, toward the transducer) and
  get the exact cone height/opening needed, with a safe/unsafe check
  against the minimum-height collapse.
- **Batch reverse-solve** — pick 0.5 or 1.45 MHz and a comma-separated
  list of target depths; get back a table of cone_height/opening_diameter
  pairs, each flagged against the collapse and max-reach limits.
- **Same-cone dual-frequency check** — for whatever cone is currently
  selected, shows the resulting focal depth at *both* 0.5 and 1.45 MHz,
  so one physical cone can be checked for use at either drive frequency
  instead of needing one cone per frequency.
- **k-Wave simulation viewer** — real field-map images (`gallery/`) for
  the nearest validated case to whatever height you land on.
- **OpenSCAD export** — a button fills a parametric sleeve/cone OpenSCAD
  template (transducer sleeve, O-ring groove, retaining lip, wraparound
  dimension labels) with the calculated height/opening/focal-distance
  values, ready to copy into OpenSCAD.

## Editing it

`cone_planner.html` is a single file: inline `<style>` for layout/theming
(light + dark mode via `prefers-color-scheme`), and inline `<script>` for
everything else. The logic that matters lives in a few functions:

- `computeModel(diameter, roc, freqMHz)` — the core model: minimum safe
  height, matched opening, focus location, per-height curve data. Uses
  the embedded `VALIDATED` k-Wave data table directly when inputs match
  the validated transducer/frequencies, and a calibrated diffraction
  model (`CAL`, `interpCalibrated`) otherwise.
- `rEndAtHeight(...)` — matched opening diameter at an arbitrary height.
- `dualFreqDepths(...)` — same-cone depth at both drive frequencies.
- `renderBatch()` — the batch reverse-solve table.
- `SCAD_TEMPLATE` — the OpenSCAD template string, with `__DIAMETER__` /
  `__HEIGHT__` / `__OPENING__` / `__FOCAL_05__` / `__FOCAL_145__`
  placeholders filled in on export.

## Where the numbers come from

The `VALIDATED` table and `gallery/` images are outputs of full k-Wave
axisymmetric simulations (source construction, beam-envelope metric,
minimum-height focal-collapse study, etc.) — the simulation pipeline,
raw results, and full methodology live in
[megan-shayna/K_wave](https://github.com/megan-shayna/K_wave)
(see its `README.md` and `RESULTS.md`). This repo is just the
calculator built on top of that data.

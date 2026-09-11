# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Course materials (not an application) for **CS456 Computer Architecture, Fall 2026, at JMU**. It holds
student-facing lab handouts written in Markdown, the screenshots those handouts embed, and the Xilinx
constraints file students download. There is no build, no test suite, and no dependency manifest —
"working on this repo" means editing prose, Verilog snippets embedded in prose, and images.

Handouts link to raw files by absolute URL under `https://github.com/cs456f26/labs`; keep that org and
repo when adding new download links.

## Layout and conventions

Each lab is a directory `labNN/` containing:

- `labNN_description.md` — the handout itself. This is the deliverable.
- `README.md` — a one-line note about what the lab covers.
- PNG screenshots referenced by the handout.

Image links are relative to the handout's own directory: bare filenames for screenshots in the same
lab (`![x](allgates.png)`), and `../labNN/` only when genuinely reaching into another lab — lab02
reuses two of lab01's force-constant screenshots this way.

Screenshot filenames are a mix of descriptive names (`allgates.png`, `finalverilog.png`) and raw
`Screenshot from YYYY-MM-DD HH-MM-SS.png` captures with spaces in them — quote paths in shell commands.
Several of the dated captures are not referenced by any handout.

## Handout structure

Every `labNN_description.md` follows the same skeleton, and new labs should keep it:

1. `# LabNN - <topic> (<vivado_project_name>)` — the parenthesized name is the Vivado project name
   students must use.
2. **Important Notes** — naming files as specified, filling in the comment header block, taking
   screenshots as you go.
3. **Submission Details** — a PDF report to Canvas, with an explicit per-item point breakdown that
   sums to 10.
4. **Learning outcomes**.
5. Numbered click-by-click Vivado walkthrough (project creation → editing files → schematic via
   `RTL Analysis -> Open Elaborated Design` → simulation → board programming), interleaved with
   fenced ```verilog blocks and screenshots.

Backtick-quote Vivado UI elements and signal names. Verilog fences are also used for TCL snippets
(there is no ```tcl in this repo).

## Hardware/toolchain facts the handouts depend on

- Board: **PYNQ-Z1**; part **xc7z020clg400-1**. Getting the part wrong causes missing-package-pin
  errors later, and the handouts warn about this explicitly — keep that warning in new labs.
- Toolchain: Xilinx **Vivado** on Windows lab machines.
- `lab02/PYNQ-Z1_C.xdc` is the shared constraints file for the whole course. Its port names are
  **already renamed to the course's conventions**: `SWITCHES[1:0]`, `LEDS[5:0]`, `BUTTONS[3:0]`.
  Student Verilog must use those exact identifiers, capitalized. Most lines are commented out;
  students uncomment only the pins they use. Later labs (lab03 onward) tell students to reuse this
  same file from lab02.
- Pedagogical arc: lab01 forces signals by hand (`add_force` / `run 10ns` in the TCL console);
  lab03 introduces testbenches (`` `timescale 1 ns/ 1 ns ``, `reg` for inputs, `wire` for outputs,
  `localparam time_step`, `#time_step` delays, `$finish()`) and explicitly frames them as the
  replacement for hand-forcing. Don't introduce testbenches into lab01/lab02 material.
- Labs emphasize **structural** Verilog (gate primitives like `and(out, a, b)` wired with `wire`),
  not behavioral. Keep examples structural unless the lab is deliberately moving past it.

## Recurring pitfall

Labs are revised each semester by copying the prior offering's files, so stale artifacts accumulate:
duplicate `_descriptionSS##.md` variants of the same handout, prior-semester org names in download
URLs, and prior-semester labels in READMEs. When editing a lab, check that its handout has no
leftover sibling copy and that semester references say Fall 2026.

## Commits

Short imperative subject lines describing the content change ("Fix links and typos in
lab02_description.md", "Add draft lab03 for fall 2026"), no body.

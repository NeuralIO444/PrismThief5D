# PrismThief5D

A film-negative scan workroom built around the Canon EOS 5D Mark II.

**Gather, develop the negative, make the print** — a tethered DSLR-scan
pipeline in three stages:

1. **Gather** — the 5D II on a copy stand, tethered over USB via
   gphoto2/libgphoto2. The roll is the unit of work: frame-mapped CR2s
   plus one rebate frame per roll.
2. **Develop** — RAW decode (LibRaw), film base sampled from the rebate,
   per-channel negative inversion modeled on darktable's negadoctor.
   Calibrated per roll, against the stocks actually shot: Tri-X 400,
   CineStill 800T, Fujichrome Astia 100, Ultramax 400.
3. **Print** — a contact sheet for the roll, non-destructive per-frame
   finish, and print-ready export presets. Everything runs locally on
   the Mac Studio.

Status: **spec stage** — no build yet. The founding document is the
[project spec](docs/spec-2026-10-16.md). First build slice, when
approved: black-and-white end to end — tether one roll of Tri-X,
develop it, print the contact sheet.

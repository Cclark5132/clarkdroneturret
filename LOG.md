# Build Log

## 2026-11-06

- Set up this repo with the BOM, build plan and log.
- Confirmed parts: 180 deg servos (2), 6 V 5 A supply and DC jacks are 5.5 x 2.5 mm, 22 AWG solid wire for all wiring, DPDT toggles for the arm and kill switches.
- MOSFET modules arrive Nov 11-12, so the fire control bench test waits for them.
- Next: Step 2A, servo bench test. Wire both servos to the 6 V supply with a common ground (pan D9, tilt D10) and write the first sketch that aims them from serial commands (`A <pan> <tilt>`, `H`, `P`). See `PLAN.md`.

## 2026-11-05

- Finished the parts list and ordered everything. See `BOM.md`.
- Dropped the lazy susan for a pan base made of 4 x 608ZZ rollers on a printed track. Cheaper and stiffer.

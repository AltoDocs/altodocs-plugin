# Analysis: A2.1 for the whole building, A2.2 member by member

A2 proves that the structure carries its loads. It takes loads, combinations
and factors from A1.1 by name and section and never derives them again.

## Which tool calculates what

| Work | Tool | Where it prints |
| --- | --- | --- |
| Vertical load takedown (bearing lines, columns, footings) | Load file: `set_line_takedown` | A live load view in A2.1 |
| Horizontal load takedown (diaphragms to stabilising walls) | Load file: `set_stability_takedown` | A live load view in A2.1 |
| One beam (any loads, several spans) | A beam block (`kind: "beam"`), analysed when saved; `update_document_beam` | A2.2 |
| Many beams of one type | One schedule block (`kind: "schedule"`, app `beam`); `update_document_schedule` | A2.2 |
| Any other calculation | A worksheet in the document (`kind: "worksheet"`), results from `evaluate_worksheet` | Where it stands |
| Frames, slabs (FE), foundations, columns, retaining walls, stringer models, strut-and-tie | In the AltoDocs app only | Their blocks in A2 |

When a member needs an app the tools cannot drive, say so, and set out what the
user must enter there (geometry, loads from the Load file, materials from
Project 271).

## A2.1 Statiske beregninger, bygværk

The chapters, each heading kept (`Ikke relevant.` with one clause where it does
not apply):

1. **Hovedstatik.** 1.1 structural system, with element IDs as on the
   drawings; 1.2 vertical load takedown to the foundations; 1.3 horizontal
   loads (wind, imperfections, seismic, earth pressure) through the diaphragms
   to the stabilising structure, and its stability; 1.4 reactions and
   equilibrium, in a real table, with where each reaction is delivered.
2. **Robusthed.** The calculations for the failure scenarios A1 lists.
3. **Almene statiske beregninger.** Calculations shared by members across
   sections.
4. **Verifikation af IKT-beregninger.** How software results were checked.
5. **Påvirkning af tilstødende bygværker.**

How to calculate:

- One bearing line, wall or diaphragm at a time, finished with its check before
  the next. Each contribution on its own line (a tributary width times an area
  load), then the sum: the checker must follow each one.
- Every horizontal load case is calculated. One not carried forward is
  justified with numbers in writing. For stability, use the smallest vertical
  load that can act with the horizontal load. Both situations (wind with
  imperfection, seismic with imperfection) are carried through.
- Close every distribution with its equilibrium check as a sentence with the
  numbers.
- The takedown itself lives in the Load file: quote `read_loads` with the name
  of the line or wall. Use a worksheet only for what the Load file does not
  hold.

## A2.2 Statiske beregninger, konstruktionsafsnit x: one member

Under a heading with the member's ID and name (`B1 Steel beam over ground
floor`), these seven parts, in this order, never one left out:

| # | Danish | English | Content |
| --- | --- | --- | --- |
| 1 | Konstruktionsdels-ID | Member ID | The ID, the same as on the drawings |
| 2 | Grundlag | Basis | Geometry, material, class, partial factors, referring to A1 |
| 3 | Statisk system | Structural system | Supports, span, what loads it |
| 4 | Laster | Loads | Every load and combination, and where each comes from |
| 5 | Snitkræfter og reaktioner | Section forces and reactions | Governing forces, reactions passed on |
| 6 | Styrke (ULS) | Strength (ULS) | Action against resistance, utilisation |
| 7 | Anvendelse (SLS) | Serviceability (SLS) | Deflection or other limit against its requirement |

- **Part 4 names the source of every load** ("q_k = 4.2 kN/m², specification
  B.10 of the Load file"). A load with no stated source fails review; ask.
- **The conclusion format is fixed:** `M_Ed = 90 kNm < M_Rd = 120 kNm → OK`, then
  `Utilisation ULS 75 % ≤ 100 % OK`; `u = 12 mm < L/300 = 20 mm → OK`. A check
  that fails reads `→ NOT OK` with its utilisation. Never soften it and never
  change an input to make it pass.
- Say where on the member each check is made. Use the member IDs the user gave.
- A beam block reports each utilisation, the deflection limit and what it did
  not check: report those. A pass is not a design approval.

## Many members of one type: a schedule

Beams that differ only in span, section and loads are one schedule block, not
a block each:

- `read_document_schedule` first: shared settings, the columns of a row, every
  row's ID.
- A row: the beam's ID as on the drawings, span in metres, section, and one
  uniform **characteristic** line load per load type in kN/m. Never a design
  load: the block combines them.
- Where the Load file assigns loads to named beams, take the rows from it
  (`loadFileId`), so every load has its source. A row taken from the Load file
  waits for its span: ask for it, or read it from the drawings.
- `lightestSections` gives every row ("all"), or the rows named, the lightest
  section that passes every check.
- Report the verdict from the answer, and name every beam that fails or is not
  calculated. `print: "annex"` also prints every beam in full at the end.
- A beam with point loads, a partial load or several spans is a beam block of
  its own.

## Worksheets

Inputs as variables with units, results as formulas of them (`M_Ed =
q_d*L^2/8`, `U = M_Ed/M_Rd`). Evaluate, and quote only evaluated values.
Itemised lists: each item its own named result, the total the sum of the names.
Python cells are saved here and run only in the app.

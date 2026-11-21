# The Load file

A project's loads live in its Load file: a file of its own that A1 prints
(chapter 6), A2 uses (load takedown, member loads) and beam blocks and
schedules take their loads from. Open the `loads` toolbox. Every load tool
except `list_load_files` and `create_load_file` names the file with
`documentId` and takes its own input in `args`.

## Before building

- `list_load_files`: a project normally keeps one. Project 271 names the one
  its documents use; `create_load_file` only when there is none. Creating an
  SBi 271 document starts one from Project 271's load facts, if needed.
- `read_loads`: what is there, with every derived value and every problem the
  file reports. `read_loads_guide` once before building more than a single row.
- `get_file_links` before a change: which documents and members follow the
  file.

## Building it, in this order

1. **Basis** (`set_loads_basis`): national annex, consequence class (Project
   271 owns it, and the file follows it), load types on and off, the
   combination set, the floor reduction (a decision: applied or not, with
   storeys and the reason).
2. **Defined loads** (`add_load_row`, `update_load_row`): densities, dead loads
   (bound and free), imposed loads by category. Every value is characteristic,
   with its source and status.
3. **Actions** (`set_load_action`): wind and snow worked out from the site; the
   site facts come from Project 271 and the user, never assumed.
4. **Special loads** (`set_action_type`): a crane, a rail vehicle, attached
   equipment or an impact is an action type with its nature, ψ and γ and the
   reference they come from. When it is one of Project 271's special loads,
   give `specialLoadId` (SL3): its status and source then follow Project 271,
   so never set them on the type. It joins the combinations once a
   specification row or reaction case is of it. Until then a combination that
   names it says it has no values yet.
5. **Load specifications** (`add_load_spec`, `add_load_spec_rows`): one per
   surface or line build-up (roof, floor, deck), citing the defined loads. This
   is what members and the report cite ("B.10 Floor slab").
6. **The load path**, which is the analysis of A2.1 ([analysis](analysis.md)):
   - a beam: `set_load_assignment` (specification and tributary width);
   - a frame: `set_load_takedown`;
   - a wall line or a column summed storey by storey to its footing:
     `set_line_takedown`;
   - the building's horizontal loads to its stabilising walls:
     `set_stability_takedown`.

A typical situation starts faster from `apply_load_preset`. Then say which of
its values the user must review.

## Rules

- Every length, height, span, tributary width, position and load not in the
  file comes from the user or their documents. Ask; never assume a typical
  value. `set_line_takedown` and `set_stability_takedown` replace the whole
  takedown: read it first and send back everything you do not change.
- Characteristic values live here. Design values are formed by the member
  (its combinations); never quote a specification's design table as a
  member's load.
- A surface load (kN/m²) becomes a line load (kN/m) only through a tributary
  width.
- An answer with `saved: true` is saved; `saved: false` changed nothing, and
  `result.error` says why. Make one change at a time and read back with
  `read_loads`: there is no version check.
- Report the governing values, each wall's utilisation and every problem the
  file lists, quoted from `read_loads`.

## Showing loads in a document

A document never types a load table. `insert_load_view` places a live view of
the Load file (basis, loads by type, wind, snow, combinations, a specification,
an assignment or takedown, the verification summary). `read_document` shows
what each view prints now. A1.1 chapter 6 is made of such views. Lead into each
with one sentence saying what it shows.

# The documents

## What each one is for

`.1` is for the whole building. `.2.x` belongs to construction section x: the
section numbers are fixed in Project 271 (A1.1 §1.3) and reused by every family.

| Code | Name | In plain words | Holds | Never holds |
| --- | --- | --- | --- | --- |
| A1.1 / A1.2.x | Konstruktionsgrundlag | Design basis: every prerequisite the design rests on | Classes, sections, site and ground, structure, materials, loads, combinations | Calculations |
| A2.1 | Statiske beregninger, bygværk | Calculations for the whole building | Structural system, vertical and horizontal load takedown, reactions and equilibrium, robustness, software verification | Member checks |
| A2.2.x | Statiske beregninger, konstruktionsafsnit | Member checks for one section | One verification per member or member type | Prerequisites restated from A1 |
| A3 | Konstruktionstegninger og modeller | Drawings and models | Form, position, dimensions, member IDs | How the structure works |
| A4 | Konstruktionsændringer | Changes during execution | Each change, its reason and its documentation | New design outside the change |
| A5 | Konstruktion som udført | As-built documentation | What was built, deviations | Design never built |
| B1 | Statisk projektredegørelse | Project statement: who did what | Parties, roles, document register, process | Technical assumptions or calculations |
| B2.1 / B2.2 | Statisk kontrolplan, projektering / udførelse | Control plans for design and for execution | What is checked, by whom, when, how | New design |
| B3.1 / B3.2 | Statisk kontrolrapport, projektering / udførelse | Control reports | What was checked and found, deviations and their follow-up | Control results nobody recorded |

## Order

A1.1 → B1.1 → B2.1.1 and B2.2.1 → A2.1 → A3.1, then per construction section
A1.2.x → A2.2.x → A3.2.x. Then control (B3) and, at handover, A5. Never a whole
package in one go: finish and check one document before the next.

## Creating one

- `list_sbi_documents` first: what the phase asks for, which exist (with
  links), and the arguments that create each. "What is missing" is answered
  from it. If the document exists, the create call is refused with its link:
  make a second only when the user asks for a new one (`again`).
- `create_sbi_document` makes the same document as Project 271's own Create: the
  SBi 271 headings, the project's naming and folder, a place in the document
  list, and live tables for every table slot. For a per-section document pass
  the section number from Project 271's section list.
- The document can be created before the facts are complete: its tables follow
  Project 271 and the Load file afterwards.
- Report: the name and link, the Project 271 and Load file it reads, the facts
  still missing (`missing`, printed as `AFVENTER`), and the sections left to
  write (`sectionsToWrite`, each with what SBi 271 asks of it). Ask for the
  missing facts in one message. A created document is a start, never finished.

## Live tables

Document information, classes, sections, parties, sources, special loads,
materials and loads are blocks that print Project 271, the material system and
the Load file whenever the document is opened, in the editor, the PDF and Word.

- Never type such a table, or a fact from one, into text. To change what it
  prints, change Project 271 or the Load file.
- `read_document` shows what each live table prints now ("It prints now: …"),
  and each reference with what it prints (`→"Afsnit 6.10.3 …"`; `→"?? …"` points
  at nothing and must be fixed).
- A table `read_document` cannot print ("the app works its rows out") is
  computed in the app: do not retype it or guess its rows.

## Writing a section

Only when the user asks, from this project's facts only:

1. Read the section's note in `sectionsToWrite`: it says what the tables
   already hold. Write only what they do not, and never a table row again as
   text.
2. Lead into each live table with one short sentence saying what it shows,
   without repeating its facts.
3. Where something is explained in a later section (a special load, a load
   plan, a crane), refer to that section's heading with a reference
   `[[ref:<heading block ID>|full]]` (`list_reference_targets` gives the IDs),
   never a typed number. Headings carry no typed number: the app numbers them.
4. Numbers come from worksheets, beam blocks, schedules or the Load file,
   quoted from what they return.
5. A missing fact is `(AFVENTER)` at that spot, with who owes it, and goes in
   your one message of questions.
6. Keep every required heading. A section that does not apply says
   `Ikke relevant.` with one clause why.

A2 cites A1 precisely: loads, combinations and values by the names A1.1 gives
them. Member IDs are the same in A2, on A3's drawings and in the control plans.

## Figures and evidence

A figure taken from a source document is a picture of the stored PDF's page
(`{fileId, page, area}`) with `sourceId` and `specialLoadId`: its source line
prints from the register. A figure only the project has (a plan, a section)
waits as an empty figure with its caption for the user to drop the file on.
Never redraw a finished drawing as a sketch.

## Before reporting a document as done

Read it back. Check every required heading is present and in order, every
reference resolves, every worksheet evaluates without errors, and every number
in a conclusion equals the evaluated one. Declarations, signatures and the
outcome of a control stay with the engineer: leave their named, dated places
empty.

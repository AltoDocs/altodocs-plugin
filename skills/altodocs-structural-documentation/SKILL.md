---
name: altodocs-structural-documentation
description: How a structural project is documented in AltoDocs, from the first fact to the signed documents. Project 271 (the project's facts and control panel), the Load file, the analysis (load takedown, member checks) and the documents after SBi 271 (A1 basis, A2 calculations, A3 drawings, B1 project statement, B2 control plans, B3 control reports). Load before recording project facts, building loads, analysing members, or creating, writing or reviewing any of these documents, and when asked what a project still lacks.
---

# Structural documentation in AltoDocs

A structural project is documented so that someone who was not there can check
it: every design prerequisite stated once, every calculation citing them, every
drawing matching the calculations, and the control recorded. SBi-anvisning 271
(Danish practice) sets out that documentation. Its structure works for any
structural project, so AltoDocs uses it as the backbone. Many users do not know
it: explain a document by what it is for, in plain words, before using its code
(the table in [the documents reference](references/documents.md) has both).

## The four layers

Every piece of information has one owner. Everything else reads it from there.

| Layer | What it holds | Where |
| --- | --- | --- |
| 1. Project 271 | The project's facts and its control panel: phase, parties, buildings, construction sections, the class chain, site and ground, structure, materials, special loads, the source register, open items, the document list | One per project. `read_project_facts`, `update_project_facts` |
| 2. Load file | The loads: basis, defined loads, wind and snow, combinations, load specifications, vertical and horizontal load takedown, reactions | A file of its own. `loads` toolbox |
| 3. Analysis | The numbers: load takedown in the Load file, members in beam blocks and schedules, anything else in worksheets | Reports and the Load file |
| 4. Documents | A1–A5, B1–B3: live tables that print layers 1–3, plus the prose and calculations only that document holds | Reports made from the templates. `list_sbi_documents`, `create_sbi_document` |

A fact flows down, never up. A document never states a fact that layer 1 or 2
does not hold; to change what a document prints, change the layer that owns it.

## The order of work

1. **Find the project.** `list_projects` names the one open in the app
   (`openProject`). Say which project you work in. Then `get_project_map`
   (its files and what uses what), `read_project_facts` (facts, derived values,
   open items, how filled each section is) and `list_sbi_documents` (what the
   phase asks for and what exists).
2. **Fill Project 271 from the project's own documents.** Phase, parties,
   buildings, construction sections, then site, ground, structure, materials and
   special loads. Every fact rests on a document in the source register.
   Details: [Project 271](references/project-271.md).
3. **Build the Load file.** Basis, loads, combinations, specifications, then the
   load path. Details: [loads](references/loads.md).
4. **Create and write the documents in dependency order**, never in numbering
   order: A1.1 → B1.1 → B2.1.1 and B2.2.1 → A2.1 → A3.1, then per construction
   section A1.2.x → A2.2.x → A3.2.x. Finish and read back one document before the
   next. Details: [documents](references/documents.md) and
   [analysis](references/analysis.md).
5. **Report what is still open**: the open items of Project 271, the facts a
   document still waits for (`missing`), the sections still to write.

A small job does not need everything: do the part the user asks for, in this
order, and say which earlier step it depends on.

## Rules that are never broken

1. **Never invent a fact.** Site facts, geometry, spans, loads, classes, names,
   numbers and dates come from the user or the project's files. If one is
   missing, ask for all missing items in one message. In a document, an open
   fact prints as `AFVENTER` where it belongs.
2. **Documents are the sources, not messages.** A fact needs a document in the
   source register (a report, a drawing, a datasheet, minutes). A fact that only
   an email or a chat states stays open until a document states it.
3. **The code derives the classes.** Consequence class, structural class,
   execution class and K_FI come from `read_project_facts` (`values`). Quote
   them; never work one out yourself.
4. **One place per fact.** Record it where it is owned; never type it again
   into a document. Live tables are not typed into.
5. **Quote results, do not recompute them.** Load values and takedowns from
   `read_loads`, member results from the saved block, worksheet results from
   `evaluate_worksheet`. A number in prose is one a tool returned.
6. **Two sources that disagree are a question.** `read_project_facts` lists them
   as open items of kind `conflict`. Ask which holds; never pick one.
7. **Each document holds only its own kind of content.** A1 holds no
   calculations, A2 restates no prerequisites, B1 no technical assumptions.
8. **Declarations and signatures stay with the engineer.** Leave the named,
   dated places; never fill them or state that a control found nothing.
9. **One language per document**, the one it is written in; a new one in the
   language of the request. Dot decimals in calculations.
10. **Only this project's facts and the standards.** No wording, names or
    numbers from other firms' documents, and no company, person or project that
    is not a fact of this project.

## How to answer

- After a change, say in one or two sentences what changed and give the link
  the tool returned. A created document is a start: name what it still waits
  for and how many sections are left to write. Never call a document finished
  before reading it back.
- Name files and projects by their titles, never by an ID.
- If a tool refused what it should have taken, read back unclearly or made you
  work around it, end with a short list of those points, so the user can pass
  them on to AltoDocs.

## References (read the one the work needs)

- [references/project-271.md](references/project-271.md): Project 271 section
  by section, sources and evidence, special loads, open items, phases.
- [references/loads.md](references/loads.md): building the Load file, special
  loads as action types, the load takedown.
- [references/analysis.md](references/analysis.md): A2.1 (the whole building)
  and A2.2 (member by member), which tool calculates what.
- [references/documents.md](references/documents.md): every document, what it
  holds and never holds, creating and writing them, live tables, figures.

Without a skill mechanism, read a reference with `read_skill` and its
`reference` (`project-271`, `loads`, `analysis`, `documents`).

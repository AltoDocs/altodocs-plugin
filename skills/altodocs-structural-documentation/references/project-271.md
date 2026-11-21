# Project 271: the project's facts and control panel

Project 271 is entered once per project, and every document prints from it. It
also keeps the project's control state: what is open, what disagrees, what the
phase still asks for. Read it before stating any fact:
`read_project_facts` (with `section` to read one part) returns:

- `form`: the facts as recorded;
- `values`: what the code derives from them (CC → KK, RC, K_FI, EXC; document
  numbers; load values from the Load file). Quote these; never derive one;
- `sections`: each section with `filled` of `fields`. Few filled means work to
  do;
- `openItems`: missing facts, open questions, contradictions (`conflict`),
  and disagreements between Project 271 and the Load file.

## Recording a fact

- Read the section first for its exact field names, types and choices.
- `update_project_facts` with `patch` for single fields. To add, change or
  remove **one row of a list** (a building, a section, an interface, a party, a
  source, a special load), use `rows` with its list path (for example
  `laster.saerlige`, or `laster.saerlige[id=SL3].spoergsmaal` for a row inside a
  row): the other rows stay. A list sent in `patch` replaces the whole list.
- Take the choice that is true for this project. Write a line of your own only
  when no choice fits. Leave a field empty when nobody has stated the fact.
- Give a `requestId` on writes; repeat it unchanged after a transport failure.
- After a change, use the `changed` groups in the answer instead of reading
  the whole form again.

## The sections, in working order

| Section | What it holds | Usually from |
| --- | --- | --- |
| 0 Projektfase, Generelt, Aktører | Phase, project name and number, client, address, the parties and their roles | The user, the contract |
| 1 Bygværker, Bygninger og bygningsdele | Each building: use, size, new or existing; its parts | Architect's drawings, the brief |
| 1.3 Konstruktionsafsnit, Grænseflader | Construction sections (numbered, reused by every later document) and the interfaces between them | The engineer's division of the work |
| 1.4 Udførelse, 1.5 Tegninger | Execution, the drawing and model register | Drawing list |
| 2 Grundlag | Standards, design life, the class inputs | The user; the code derives the classes |
| 3 Forundersøgelser | Site, ground, groundwater, existing and neighbouring structures | Geotechnical report, surveys |
| 4 Konstruktioner | Static principles, how the structure works, requirements, robustness, fire, temporary states, maintenance | The engineer |
| 5 Materialer | Materials and their properties | The engineer, product data |
| 6 Laster | Load facts the Load file starts from (terrain, snow zone, geometry) | Site, drawings |
| Særlige laster og udstyr | Special loads (a crane, a tram, an impact) and equipment with loads | Other parties' documents |
| Kilderegister | Every source document | The project's files |
| B1, B2, B3 sections | Project process, control plans, deviations | The engineer, the contractor |

## Every fact rests on a document

- Each source is a row of the source register (`kilder.liste`): ID, title,
  revision, date, status, and `fil`, the ID of the project file. A file the user
  attached goes into the project first (`request_file_upload`, then
  `add_project_file`); a Word file is added as the PDF its author issued, never
  converted by you.
- Read stored PDFs with `search_project` and `read_file_text`, and name the file
  and page of every fact taken from one. When two files disagree, or the only
  source is old, ask which holds before recording it.
- An email or a chat message is not a source. A fact it states stays open until
  a document states it: ask for the document.
- Show a source's table or drawing as **evidence**: a picture block whose source
  is the stored PDF's page (`{fileId, page, area}`), with `sourceId` (the
  register entry) and, for a special load, `specialLoadId`. The figure prints its
  source line from the register, and the special load's section lists it. Never
  type a "Kilde:" line.

## Special loads

A crane, a tram, an overhead line, an impact on a buffer stop: each is a row of
`laster.saerlige` with its ID (SL1, SL2 …), type, what it acts on, source,
status (Oplyst, Antaget, Afventer, Godkendt) and who answers for it.

- A decision still owed is an **open question** of its own
  (`laster.saerlige[id=…].spoergsmaal`, status Åben until Besluttet, with the
  decision). It stays an open item after the data has arrived.
- A1.1 prints each special load as a section of its own (type, source, status,
  responsible, open questions, decisions, the figures that document it). Never
  write those facts as text.
- In the Load file its action type names it (`specialLoadId`): its status and
  source then follow Project 271 ([loads](loads.md)).

## Open items and contradictions

`openItems` is the project's to-do list: answer it before writing prose. A
`conflict` is two places stating different values (a building's type against
the pre-investigation, the Load file's wind against the terrain category): ask
which holds and record the answer where it is owned.

## Phases and classes

The phase decides which documents are owed (`list_sbi_documents`):

| Phase | Documents it adds |
| --- | --- |
| Dispositionsforslag, Projektforslag | A1.1, A2.1, A3.1, B1.1 (preliminary) |
| Myndighedsprojekt (building permit) | A1.1 as the permit's start edition; for KK2–KK4 also B2.1.1, B2.2.1, B3.1.1 and the starterklæring |
| Udbudsprojekt, Udførelsesprojekt | Per construction section A1.2, A2.2, A3.2, B1.2 (KK2–KK4 also B2.1.2, B3.1.2); A4.1 |
| Udførelse | Control of the works: B3.2.1, B2.2 and B3.2 per work |
| Aflevering | A5 per work and the sluterklæring |

The structural class (KK1–KK4) decides how much control and third-party
checking the project needs. Quote it from `values`.

## Two Project 271 files

A project can hold more than one (a copy made to try a variant). Reading uses
the project's own, the oldest, and lists them all. Before changing one or
creating a document, ask the user which is meant, naming them by title.

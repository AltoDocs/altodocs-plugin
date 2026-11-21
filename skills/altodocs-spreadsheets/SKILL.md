---
name: altodocs-spreadsheets
description: Create and edit saved native AltoDocs spreadsheet workbooks, tabs, tables, cell values, formulas, formatting, layout, dropdowns and colour rules from conversation data, and read formula results. Use when the requested result is a standalone spreadsheet, rather than a MathCanvas sheet or report table.
---

# Native spreadsheet files

Use the available spreadsheet tools for the requested standalone workbook.
`create_spreadsheet` creates named tabs; `list_spreadsheet_sheets` returns their
real IDs; `read_spreadsheet` and `edit_spreadsheet` operate on saved cells. Do not
substitute a report table or MathCanvas sheet without the user's agreement.
If these tools are absent in the host/grant, state the missing capability.

Use conversation data or values the host has actually extracted from an
authorised attachment. Keep unknown cells unknown and source/formula assumptions
explicit. This tool surface does not import an XLSX binary or fetch an external
workbook. Resolve the destination with project/file discovery; use the workspace
workflow when project/folder creation or a file lifecycle operation is requested.

For a new file, use new request/document UUIDs, a meaningful title and unique tab
names. After creation, obtain tab IDs with `list_spreadsheet_sheets`. For an
existing file, read its saved content version and target range first. Omit
`sheetId` only if there is exactly one tab.

Cell/range coordinates are zero-based. Use ascending bounded ranges, not an
entire million-row sheet; `read_spreadsheet`, clear and format ranges are
limited to 20,000 cells. Write a table (imported data, a schedule, a list) with
one `write_spreadsheet_table` call: rows of values, or CSV text, from a start
cell. A string starting with `=` is a formula. Use `set_spreadsheet_cells`
for a few scattered cells only (at most 2,000 per batch); an edit batch has at
most 100 operations. Write either a value or a formula for a cell, never both.
Preserve untouched cells and tabs. Use add/rename/duplicate/delete tab
operations only as requested.

Format after writing, a range at a time: `format_spreadsheet_range` (font,
colours, number format, horizontal/vertical alignment, wrap, borders on each
cell or only the outline), `set_spreadsheet_layout` (column widths and row
heights in pixels, merge/unmerge, freeze the first rows/columns),
`add_spreadsheet_dropdown` (a list of allowed values, e.g. Status:
Planlagt / I gang / Afsluttet) and `add_spreadsheet_color_rule` (colour cells
by text, number, blank or a formula). A rule answers with its `ruleId`;
`read_spreadsheet` lists the sheet's layout, dropdowns and colour rules, and
`remove_spreadsheet_rules` removes rules by id.

**Formulas are saved, not calculated, when written.** To check results, read
with `recalculate: true`: the app's own spreadsheet engine calculates the
workbook and each formula cell comes back with its `result` (an error such as
`#DIV/0!` included). The saved file is not changed by that read. A saved `v`
of a formula cell is a cache and is not proof of a result. Report results from
a recalculated read only. Do not claim arbitrary Excel compatibility, charts,
pivots or filters.

Use the latest `expectedVersion` for writes. Retry a lost response with identical
UUID and payload; re-read after conflicts. After saving, verify the affected
range and tab list. Return the actual spreadsheet URL and a short description
of the saved source changes. Retain the same workbook on follow-up edits unless
the user requests a copy.

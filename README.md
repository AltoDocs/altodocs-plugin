# AltoDocs

Create engineering documents and editable MathCanvas calculations from chat.
This companion plugin bundles eight workflow skills with one remote AltoDocs
MCP connector. It contains instructions and connection metadata, not the
AltoDocs application or server implementation.

## Start with a saved calculation

Connect your AltoDocs account from the plugin's Connectors tab and select the
projects it may access. Sign in through AltoDocs; never paste credentials into
chat. Your Claude plan and workplace administrator must allow the plugin and
connector. Use your own AltoDocs account, not reviewer credentials.

> In my selected project, create a standalone MathCanvas named Load worksheet.
> Add q = 12 kN/m, L = 6 m and M = q*L^2/8, with a note stating assumptions.
> Save it, read it back, evaluate M in kN m and give me the worksheet link.

For these formulas the result is 54 kN m. Then ask to change L to 7 m in the
same file, recalculate and tidy the layout; the result is 73.5 kN m. Follow the
returned native link to inspect and edit the saved sheet in AltoDocs.

## Included workflows

- Standalone MathCanvas: inputs, dependent formulas, units, notes, layout,
  saved evaluation and follow-up revisions.
- Engineering reports: text, tables, embedded calculations, supported beam
  analysis, targeted edits and native section links.
- Markup: editable blank-background sketch blocks and saved PNG previews.
- Native 2D CAD: geometry, layers, blocks, annotations, layouts and viewports.
- Spreadsheets: saved cell values, formula source, styles and tabs.
- Workspace: projects, folders, file copy/move/rename, recoverable Trash/restore.
- Calculations: ordered unit-aware evaluation without implying a saved file.
- Getting started: account connection, tool availability and workflow selection.

## Source files and permissions

You can supply calculation notes or PDF, Word and spreadsheet attachments that
Claude can read. File interpretation depends on the host. AltoDocs does not
fetch or parse arbitrary attachments itself. Missing values and assumptions
must be resolved before claiming a complete result.

The endpoint is https://app.altodocs.io/mcp and uses OAuth with dynamic client
registration. User/project permissions still apply. The service supports
`documents:read`, `documents:write`, `calculations:evaluate` and `projects:create`;
existing connections may have fewer scopes. A new project needs explicit
project-creation consent. Standalone MathCanvas is free to use; other file
types require the appropriate AltoDocs editing access.

An installed skill does not make a missing tool available. Check which tools
Claude exposes before testing standalone sheets or CAD. Directory review,
server capabilities and individual connection permissions are separate. The
connector's directory review currently captures the original seven tools;
the expanded native server has 116 scoped tools. Do not infer that every one
is available through a particular directory installation.

## Limits and verification

Spreadsheet formulas are stored without connector recalculation. Markup cannot
use arbitrary uploaded-image/PDF backgrounds. CAD has no external DWG import
or PDF plotting through these tools. The connector does not run user Python,
export files, send email, administer sharing, or certify engineering designs.
Report worksheets, beam models and sketches are not automatically linked.

Native server verification on 22 October 2026 passed focused persistence and
calculation tests and a synthetic public-HTTPS OAuth/workspace proof. It does
not establish skill activation in every Claude surface, a fresh uploaded
PDF/Word conversation, full-size drawing acceptance or individual execution of
all CAD commands. Test with nonconfidential sample data before project use.

## Installation and support

For personal testing, upload the ZIP through Customize > Plugins > Add >
Upload plugin. A directory listing becomes installable only after Anthropic's
review and publication. Workplaces may require an Owner to add the connector
or approve the plugin before members connect their own accounts.

Documentation: https://altodocs.io/docs#section-mcp
Setup: https://altodocs.io/integrations
Privacy: https://altodocs.io/privacy
Terms: https://altodocs.io/terms
Support: contact@altodocs.io

Only the selected tool arguments and requested native content pass through
the connector; it does not request complete conversations or memories.
Disconnect in Claude's connector settings to remove the connection. Never
include passwords, tokens or confidential documents in a support request.

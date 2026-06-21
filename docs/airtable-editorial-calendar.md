# Airtable Editorial Calendar — Spec

This is the canonical spec for the Marketing OS editorial calendar in Airtable. Claude reads this file whenever the user wants to schedule, view, or update content in the calendar.

## Base

- **Default name:** `Marketing OS — Editorial Calendar`
- **Discovery aliases:** also match `Editorial Calendar`, `Content Calendar`, `Content calendar`
- **Single source of truth:** never create a duplicate. Always check first by listing bases and looking for one that matches the names above.

If the team already maintains a content calendar in Airtable, **use the existing one** instead of creating a new base. Replace the IDs below the first time you connect (Claude can fetch them via `list_bases` → `list_tables_for_base` → `get_table_schema`):

```
Base ID:   <fill in after first connection>
Table ID:  <fill in after first connection>
```

## Default Table: `Editorial Calendar`

If creating a new base, set up the table with these fields. If using an existing team table, **use its real fields and select options** — don't invent new ones.

| Field | Type | Required | Description |
|---|---|---|---|
| `Title` (or `Name`) | Single line text | yes | e.g., "Signals Launch — LinkedIn Post" |
| `Program` | Single line text | yes | Program/campaign name (must match the brief in `briefs/`) |
| `Channel` (or `Channels`) | Single or multi-select | yes | `LinkedIn`, `X`, `Blog`, `Email`, `Facebook`, `Sales Enablement` (or the team's existing options) |
| `Status` | Single select | yes | `Planned`, `Drafted`, `In Review`, `Approved`, `Changes Requested`, `Published` (or the team's existing lifecycle) |
| `Due date` (or `Target Publish Date`) | Date | yes | From the brief's activity start date or per-piece schedule |
| `Owner` | Single line text | no | Person responsible (blank by default) |
| `Link` (or `Content Link`) | URL | no | Path to the file in `output/` or a public URL once published |
| `Key Messages` | Long text | no | Pulled from the brief's key messages field |
| `Notes` | Long text | no | Extra context (image prompt, dependencies, blockers) |

### Status Lifecycle (default)

```
Planned → Drafted → In Review → Approved → Published
                          ↓
                  Changes Requested → Drafted (loop back)
```

If the team's base uses different status names (e.g., `For Review`, `Approved by [Name]`, `Posted`), map the Marketing OS lifecycle to those names instead of renaming the team's options.

## Workflow — Check, Create, or Add

When the user asks anything about the editorial calendar, follow this order:

### 1. List existing bases
Call the Airtable MCP's `list_bases` (or `search_bases`). Look for any of the discovery aliases above.

### 2. If found
- Confirm it has a calendar-like table. Use `get_table_schema` to read the real fields and select options.
- Use the team's real field names and IDs — don't try to rename or restructure their table.
- Add or update rows there.

### 3. If not found
- Create the base `Marketing OS — Editorial Calendar` (via `create_base`).
- Create the `Editorial Calendar` table with every field above in the order shown.
- Pre-populate the single-select options for `Channel` and `Status` as listed.

### 4. Adding rows (insert)
For each planned piece from the campaign brief, insert a row:
- `Title` / `Name` — `<Program Name> — <Channel> Post`
- `Program` — from the brief
- `Channel` — the channel for this piece
- `Status` — `Drafted` (or `Planned` if the content isn't generated yet)
- `Due date` — **REQUIRED for every row.** See rules below.
- `Link` — relative path like `output/content/<program>/linkedin-post.md`
- `Key Messages` — copy the brief's key messages
- `Notes` — anything notable

If a needed Channel or Status option doesn't exist in an existing team base, ask the user before adding a new select option (these are team-owned).

### Due Date Is Mandatory

Every row added to the editorial calendar **must** have a `Due date`. Never insert a row with a blank or null `Due date`. Resolve it in this order:

1. **Use the brief's activity start date** if one is set.
2. **If the brief has no date,** ask the user before inserting: *"What date should I set for [piece name]?"* — accept a specific date, or a relative one ("next Tuesday", "end of week") and resolve it.
3. **If multiple pieces are in one brief and no per-piece schedule is given,** propose a sensible cadence (e.g. one per business day starting from the activity start date) and confirm before inserting.
4. **Never default silently to today.** Always confirm.

### 5. Updating rows
Match by `Program` + `Channel` (or by `Title`/`Name` if those aren't both present). If multiple rows match, ask the user which to update.

Common updates:
- Sent for review → status becomes `In Review` (or the team's equivalent)
- Review approved → status becomes `Approved` (or the team's equivalent)
- Review requested changes → status becomes `Changes Requested`, append notes
- Content published → status becomes `Published` / `Posted`, update `Link` to the live URL

## Failure Modes

- **No Airtable MCP connected:** tell the user *"I don't see an Airtable MCP connected. Connect one and I'll set up your editorial calendar."* Never fall back to creating a placeholder file.
- **MCP connected but no permission to list bases:** tell the user the exact error and what permission to grant.
- **Multiple bases match the aliases:** ask the user which one to use.
- **Schema drift in an existing base:** add missing fields only with confirmation; do not rename or delete existing fields.
- **Select option missing:** never silently create new `Status` / `Channel` options on a team-owned base — ask first.

## Hooks into the Marketing OS Workflow

- **Step 7 of campaign workflow** (`CLAUDE.md`) → insert rows
- **Content Review Workflow** (`CLAUDE.md`) → update status when reviews complete
- **Publish time** → final status update to `Published` / `Posted`

## Out of Scope

- This spec does not cover analytics, dashboards, or reporting views inside Airtable. Those can be added by the user later without breaking the workflow.
- This spec does not auto-publish content to channels. Publishing is manual or handled by a separate tool.

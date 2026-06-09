# Airtable Editorial Calendar — Spec

This is the canonical spec for the Marketing OS editorial calendar in Airtable. Claude reads this file whenever the user wants to schedule, view, or update content in the calendar.

## Base

- **Base name:** `Marketing OS — Editorial Calendar`
- **Discovery alias:** also match `Editorial Calendar` if found (some teams shorten the name)
- **Single source of truth:** never create a duplicate base. Always check first.

## Table: `Editorial Calendar`

| Field | Type | Required | Description |
|---|---|---|---|
| `Title` | Single line text | yes | e.g., "Signals Launch — LinkedIn Post" |
| `Program` | Single line text | yes | Program/campaign name (must match the brief in `briefs/`) |
| `Channel` | Single select | yes | `LinkedIn`, `X`, `Blog`, `Email`, `Facebook`, `Sales Enablement` |
| `Status` | Single select | yes | `Planned`, `Drafted`, `In Review`, `Approved`, `Changes Requested`, `Published` |
| `Target Publish Date` | Date | yes | From the brief's activity start date or per-piece schedule |
| `Owner` | Single line text | no | Person responsible (blank by default) |
| `Content Link` | URL | no | Path to the file in `output/` or a public URL once published |
| `Key Messages` | Long text | no | Pulled from the brief's key messages field |
| `Notes` | Long text | no | Extra context (image prompt, dependencies, blockers) |

### Status Lifecycle

```
Planned → Drafted → In Review → Approved → Published
                          ↓
                  Changes Requested → Drafted (loop back)
```

## Workflow — Check, Create, or Add

When the user asks anything about the editorial calendar, follow this order:

### 1. List existing bases
Call the Airtable MCP's list-bases tool. Look for `Marketing OS — Editorial Calendar` or `Editorial Calendar`.

### 2. If found
- Confirm it has the `Editorial Calendar` table with the schema above.
- If the schema is missing fields, add them (don't break existing data).
- Add or update rows here.

### 3. If not found
- Create the base `Marketing OS — Editorial Calendar`.
- Create the `Editorial Calendar` table with every field above in the exact order shown.
- Pre-populate the single-select options for `Channel` and `Status` as listed.

### 4. Adding rows (insert)
For each planned piece from the campaign brief, insert a row:
- `Title` — `<Program Name> — <Channel> Post`
- `Program` — from the brief
- `Channel` — the channel for this piece
- `Status` — `Drafted` (or `Planned` if the content isn't generated yet)
- `Target Publish Date` — from the brief
- `Content Link` — relative path like `output/content/<program>/linkedin-post.md`
- `Key Messages` — copy the brief's key messages
- `Notes` — anything notable

### 5. Updating rows
Match by `Program` + `Channel`. If multiple rows match, ask the user which to update.

Common updates:
- Review approved → set `Status` to `Approved`
- Review requested changes → set `Status` to `Changes Requested`, append notes
- Content published → set `Status` to `Published`, update `Content Link` to the live URL

## Failure Modes

- **No Airtable MCP connected:** tell the user *"I don't see an Airtable MCP connected. Connect one and I'll set up your editorial calendar."* Never fall back to creating a placeholder file.
- **MCP connected but no permission to list bases:** tell the user the exact error and what permission to grant.
- **Multiple bases match the alias:** ask the user which one to use.
- **Schema drift in an existing base:** add missing fields; do not rename or delete existing fields without confirmation.

## Hooks into the Marketing OS Workflow

- **Step 7 of campaign workflow** (`CLAUDE.md`) → insert rows
- **Content Review Workflow** (`CLAUDE.md`) → update status when reviews complete
- **Publish time** → final status update to `Published`

## Out of Scope

- This spec does not cover analytics, dashboards, or reporting views inside Airtable. Those can be added by the user later without breaking the workflow.
- This spec does not auto-publish content to channels. Publishing is manual or handled by a separate tool.

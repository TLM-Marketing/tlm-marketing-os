# Airtable Editorial Calendar — Spec

This is the canonical spec for the Marketing OS editorial calendar in Airtable. Claude reads this file whenever the user wants to schedule, view, or update content in the calendar.

## Base (live — use this one)

- **Base name:** `Content calendar` (TLM's live marketing content calendar)
- **Base ID:** `app7ZvIagvBSN2BmA`
- **Table name:** `📚 Content calendar`
- **Table ID:** `tblTspUmthYXWLmvt`
- **Single source of truth:** this is the canonical editorial calendar. Do not create a new base or table — add and update rows here.

## Table: `📚 Content calendar`

This is an existing team table; use its real fields and select options (don't invent new ones).

| Field | Type | Field ID | Notes |
|---|---|---|---|
| `Name` | Single line text | `fldFbSZsKSugYJ9Mm` | Title of the piece, e.g. "AI GTM in 2026 — LinkedIn Post" |
| `Status` | Single select | `fldOqiXNXajpHivYA` | `Planning`, `In progress`, `For Nati's Review`, `Text approved, waiting for graphics`, `Approved by Nati`, `On Hold`, `Posted` |
| `Link` | Single line text | `fldmOwB6djYEdnFwo` | Path to the file in `output/` or a public URL once posted |
| `Text` | Long text | `fldUSzqs3PgeKOL7W` | The post copy |
| `Image` | Attachments | `fldMIyN6hulQ7zJDS` | Generated/approved image asset |
| `Due date` | Date | `fldvacv84CbEYGMxo` | From the brief's activity start date or per-piece schedule |
| `Channels` | Multiple selects | `fldQzpluizaVl7mcf` | `LinkedIn - Caroline`, `LinkedIn - Elad`, `LinkedIn - Elia`, `LinkedIn - Company` |
| `Post Time (Israel)` | Single line text | `fldFQykZjdU8V6SH7` | Scheduled local post time |
| `Pain Points` | Single line text | `fldIXRxxsvzPARD6K` | Key pain points the piece addresses |
| `Target Audience` | Single select | `fldBjEjIIHZHK6zYl` | `Data Engineer`, `C-Level Exec (Ops)` |

Related tables in the same base (read-only context for now): `📈 Campaigns`, `💲 Results`, `Resources`, `Content pipeline`.

### Status Lifecycle

```
Planning → In progress → For Nati's Review → Approved by Nati → Posted
                              ↓
              Text approved, waiting for graphics (or On Hold)
```

Mapping from the Marketing OS review workflow: `review/pending` → `For Nati's Review`; `review/approved` → `Approved by Nati`; `review/changes-requested` → back to `In progress`; published → `Posted`.

## Workflow — Add or Update

This base already exists and is in active use. Never create a new base or table — read and write rows in `📚 Content calendar` (base `app7ZvIagvBSN2BmA`, table `tblTspUmthYXWLmvt`).

### Adding rows (insert)
For each planned piece from the campaign brief, insert a row:
- `Name` — `<Program Name> — <Channel> Post`
- `Status` — `Planning` (or `In progress` once drafting starts)
- `Channels` — map to a real option (`LinkedIn - Company` for brand posts; the named options for individual authors)
- `Target Audience` — map to an existing option (`C-Level Exec (Ops)` or `Data Engineer`)
- `Due date` — **REQUIRED for every row.** See rules below.
- `Link` — relative path like `output/content/<program>/linkedin-post.md`
- `Text` — the post copy once generated
- `Pain Points` — the key pain the piece addresses

If a needed Channel or Target Audience option doesn't exist, ask the user before adding a new select option (these are team-owned).

### Due Date Is Mandatory

Every row added to the editorial calendar **must** have a `Due date`. Never insert a row with a blank or null `Due date`. Resolve it in this order:

1. **Use the brief's activity start date** if one is set.
2. **If the brief has no date,** ask the user before inserting: *"What date should I set for [piece name]?"* — accept a specific date, or a relative one ("next Tuesday", "end of week") and resolve it.
3. **If multiple pieces are in one brief and no per-piece schedule is given,** propose a sensible cadence (e.g. one per business day starting from the activity start date) and confirm before inserting.
4. **Never default silently to today.** Always confirm.

### Updating rows
Match by `Name`. If multiple rows match, ask the user which to update.

Common updates:
- Sent for review → set `Status` to `For Nati's Review`
- Review approved → set `Status` to `Approved by Nati`
- Review requested changes → set `Status` to `In progress`
- Graphics pending → set `Status` to `Text approved, waiting for graphics`
- Content published → set `Status` to `Posted`, update `Link` to the live URL

## Failure Modes

- **No Airtable MCP connected:** tell the user *"I don't see an Airtable MCP connected. Connect one and I'll set up your editorial calendar."* Never fall back to creating a placeholder file.
- **MCP connected but no permission:** tell the user the exact error and what permission to grant.
- **Base/table not found:** confirm the IDs above are still valid; ask the user for the current base link before writing anything.
- **Select option missing:** do not silently create new `Status`/`Channels`/`Target Audience` options — ask the user first, since this is a team-owned base.

## Hooks into the Marketing OS Workflow

- **Step 7 of campaign workflow** (`CLAUDE.md`) → insert rows
- **Content Review Workflow** (`CLAUDE.md`) → update status when reviews complete
- **Publish time** → final status update to `Posted`

## Out of Scope

- This spec does not cover analytics, dashboards, or reporting views inside Airtable. Those can be added by the user later without breaking the workflow.
- This spec does not auto-publish content to channels. Publishing is manual or handled by a separate tool.

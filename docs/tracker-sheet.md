# Tracker Sheet spec

The Google Sheet is the user's only dashboard and the source of truth. `/find-jobs` adds jobs to it, `/apply` updates it, and the user can edit it by hand at any time. This file is the contract all three follow.

Test Sheet built from this spec (dummy data only): https://docs.google.com/spreadsheets/d/1rABkXyB0sG4Dho64axx-gzngCgZf5_bakJimmQt7Hos/edit

## Tabs

### Jobs tab

One row per job. Row 1 is a bold, frozen header. Columns, in this order:

| Col | Name | Allowed values |
|---|---|---|
| A | Status | Dropdown, exactly: New, Saved, Applied, Interviewing, Offer, Passed, Closed |
| B | Fit | Whole number 1 to 10 |
| C | Type | Dropdown, exactly: Staff, Fellowship, Freelance, Nearby field |
| D | Job title | Text |
| E | Org | Text |
| F | Location | Text |
| G | Why it fits | One or two plain sentences |
| H | Source | Text. Examples: Axios, Semafor, NPR, CQ Roll Call, Washington Post, WAMU, JournalismJobs, Punchbowl, The Dispatch, The 19th, Mediabistro, Media Job Board, LinkedIn alert, Pasted link |
| I | Posted | Date (yyyy-mm-dd) if the source gives one, blank otherwise |
| J | First seen | Date (yyyy-mm-dd) the scan first found the job |
| K | Link | URL of the posting |
| L | Folder | Link to the application folder. Written by `/apply`. Blank until then |
| M | Notes | Free text. LinkedIn alert jobs carry "limited info" here (no description is available) |

### Scan log tab

One row per `/find-jobs` run. Row 1 is a bold, frozen header.

| Col | Name |
|---|---|
| A | Date |
| B | Sources checked |
| C | Sources that failed |
| D | New jobs added |

## Dedupe: how /find-jobs decides a job is already known

Before adding anything, read the whole Jobs tab fresh. A found job is **known** if either test matches an existing row, in any status:

1. **Link or source job ID.** Same Link (compare after stripping tracking query strings like `utm_*`, and ignore a trailing slash), or the same source job ID when the source has one (for example the number in a LinkedIn or JournalismJobs URL).
2. **Org + Job title + Location.** Same values after lowercasing, trimming spaces and ignoring punctuation. This catches two cases the first test misses: LinkedIn repeats one role once per location, and some sources mint a new ID or URL when a job is reposted.

Rules for known jobs:

- Never add a known job again. No second row.
- If the known row has Status **Passed** or **Closed**, it stays hidden. Do not touch Status and do not resurface it.
- If the posting changed (salary now shown, or it was reposted), do not add a row. Append a short dated note to that row's Notes, for example `Reposted 2026-10-24` or `Salary now listed: ...`. Keep the existing Notes text and add to the end. Leave Status alone.

## The 14-day window

A job is eligible only if it is 14 days old or less. Age counts from **Posted** when the source gives a date, otherwise from **First seen** (today, for a brand-new find, so it is always eligible on the day it is found and ages out later). Jobs older than 14 days are not added.

## Up to 10 new jobs per run

`/find-jobs` adds at most 10 new rows per run, taking the highest Fit first. Extras beyond 10 are still added, with Notes `more`, so they are recorded and never re-found as "new" tomorrow.

Suggested better approach (the builders may adopt it): count only rows with Status New against the cap of 10, and sort the extras to the bottom with Notes `more`. The reason is that the dedupe test needs every found job in the Sheet, otherwise the same overflow jobs reappear each day and crowd out fresh ones. The user can then clear `more` or set Passed when he looks at them.

## What /find-jobs writes per run

Exactly this, and nothing else:

1. **New rows** on the Jobs tab, appended below the last row. Each new row has: Status `New`, Fit, Type, Job title, Org, Location, Why it fits, Source, Posted (blank if unknown), First seen (today), Link, Folder blank, Notes (`limited info` for LinkedIn alert jobs, `more` for overflow, otherwise blank).
2. **Notes additions** on existing rows only for the "posting changed" case above.
3. **One row on the Scan log tab**: Date (today), Sources checked, Sources that failed (blank if none), New jobs added (count of rows added this run, including `more` rows).

It never edits Status, Fit, Type, Folder or any other cell of an existing row, and never deletes or reorders rows.

## What /apply writes

Only two cells on the one row for the job being applied to:

- **Status** (column A): set to `Applied` once the package is ready or sent, as the skill defines. Never set it backwards on a row the user has moved further along (for example do not change Interviewing to Applied).
- **Folder** (column L): the link to the application folder.

Find the row by Link (or Org + Job title + Location). Write only the cells above. Do not rewrite the whole row, because that would overwrite anything the user typed.

## The user edits the Sheet too

The user can change Status from the dropdown and edit Notes, Fit, or anything else at any time. Therefore:

- Every run of `/find-jobs` and `/apply` reads the Sheet **fresh** before deciding anything. Never rely on a copy from an earlier run or earlier in the conversation.
- Never overwrite a Status the user set. If a skill wants a status change and the current Status is not what it expects, leave it and say so in the reply.
- Write single cells, not whole rows, to existing rows.
- Read by header name, not by fixed column letter, in case the user adds a column of his own. Add new data only in the columns defined here.

## How onboarding creates his Sheet

**Recommendation: (a) Claude creates it from scratch through the connector.**

It worked end to end in the test (three tool calls to build both tabs, dropdowns and colours), needs no template link to host or keep shared, and the Sheet lands in his own Drive owned by him. Option (b) would need a public or shared template link that can break or be edited by others, and adds a manual "Make a copy" step for a beginner.

Prerequisite: the Google Drive and Google Sheets connectors must both be turned on in his Claude. Drive creates the file, Sheets edits it. If Sheets is off, onboarding must stop and ask him to enable it.

## Connector calls that worked

Tool names below are for the Claude connectors used in the test. The long prefix differs per install, so match on the suffix.

### 1. Create the empty Sheet (Drive connector)

`create_file` with `title` = `Job tracker` (the test used `TEST - Nihal job tracker (dummy data)`) and `contentMimeType` = `application/vnd.google-apps.spreadsheet`. No content. Returns `id` (the spreadsheet ID) and `viewUrl`. Keep the ID; skills should store the Sheet URL or ID in the repo's profile or config so they can find it again.

New Sheet has one tab, `sheetId` 0, named `Sheet1`.

### 2. Tabs and frozen headers (Sheets connector)

`update_spreadsheet` with `spreadsheetId` and `requests`:

- `updateSheetProperties` on sheetId 0: `properties` `{sheetId:0, title:"Jobs", gridProperties:{frozenRowCount:1}}`, `fields` `"title,gridProperties.frozenRowCount"`.
- `addSheet` with `properties` `{sheetId:1, title:"Scan log", gridProperties:{rowCount:500, columnCount:4, frozenRowCount:1}}`.

### 3. Headers and data (Sheets connector)

`update_values` with `range` `Jobs!A1:M9` and `values` as a 2D array. **The range must match the data size exactly.** A range of A1:M8 with 9 rows fails with "tried writing to row [9]". Scan log: `range` `'Scan log'!A1:D3` (quote tab names with spaces).

### 4. Formatting, dropdowns, colours (one `update_spreadsheet` call)

All of this went in one batch and all of it worked:

- Header: `repeatCell` on row 0, `fields` `"userEnteredFormat.textFormat.bold,userEnteredFormat.backgroundColor,userEnteredFormat.verticalAlignment"`.
- Column widths: `updateDimensionProperties`, `dimension` `COLUMNS`, `properties.pixelSize`, `fields` `"pixelSize"`. Used: Status 100, Fit 50, Type 110, Job title 230, Org 180, Location 130, Why it fits 320, Source 130, Posted 90, First seen 90, Link 260, Folder 260, Notes 220.
- Wrap text on Why it fits (`userEnteredFormat.wrapStrategy` `WRAP`).
- Date format on Posted and First seen (and Scan log Date): `numberFormat` `{type:"DATE", pattern:"yyyy-mm-dd"}`.
- Status dropdown: `setDataValidation`, range column A rows 2 to 1000 (`startRowIndex` 1, `endRowIndex` 1000, `startColumnIndex` 0, `endColumnIndex` 1), rule `{condition:{type:"ONE_OF_LIST", values:[{userEnteredValue:"New"}, ...]}, strict:true, showCustomUi:true}`.
- Type dropdown: same, column C.
- Fit check: `setDataValidation` on column B with `NUMBER_BETWEEN` `1` and `10`, `strict:true`.
- Status colours: one `addConditionalFormatRule` per status, `condition.type` `TEXT_EQ`, range column A rows 2 to 1000, `format.backgroundColor`. Colours: New light blue, Saved light yellow, Applied light green, Interviewing light purple, Offer stronger green, Passed and Closed grey.

### 5. Read (Sheets connector)

`get_values` with `range` `Jobs!A1:M1000`. Returns displayed strings, so Fit comes back as `"9"` and dates as `"2026-10-20"`. Trailing empty cells in a row are dropped, so rows are ragged: a row with no Folder and no Notes comes back with only 11 items. Rows read as `[]` when empty. Always map values to headers by position, and treat missing trailing cells as blank.

### 6. Append a scan's new job (Sheets connector)

`append_values` with `range` `Jobs!A1` and `values` `[[...13 cells...]]`. It found the table and wrote to the next empty row (`updatedRange` `Jobs!A10:M10`). Send all 13 cells in column order, using `""` for blank ones. Scan log works the same: `range` `'Scan log'!A1`.

### 7. Change Status and Folder as /apply would (Sheets connector)

Two single-cell `update_values` calls, so nothing else in the row is touched:

- `range` `Jobs!A3`, `values` `[["Applied"]]`
- `range` `Jobs!L3`, `values` `[["https://..."]]`

Find the row number first from a fresh `get_values` read (row index in the result plus 1 for the header).

### 8. Verify (Sheets connector)

`get_values` on the changed range. To check dropdowns or date types on a row: `get_spreadsheet` with `includeGridData:true`, `ranges` for the cells, and `fields` such as `sheets.data.rowData.values.dataValidation` (full paths, no parentheses).

## Things that were awkward

- **Appended rows inherit dropdowns and colours** because the validation and conditional format were applied to rows 2 to 1000 up front, not just to filled rows. Verified on the appended row: Status, Fit and Type validation present and the date format applied. After row 1000 they stop. If the Jobs tab ever passes 999 rows, extend the ranges (or archive old rows).
- **Write calls parse like the Sheets UI.** `"2026-10-20"` becomes a real date, `"9"` becomes a number. Good for Posted, First seen and Fit. Text that looks like a number, date or formula must be sent with a leading apostrophe. Job titles like `"1/2 time editor"` or an Org like `"2026"` could become dates or numbers, so prefix risky text values with `'`.
- **`update_values` range must match the data shape exactly.** Too small a range is an error.
- **Reads drop trailing blanks**, see call 5.
- **Link column** holds plain URLs, not formulas. Sheets makes them clickable on its own.
- Conditional colours are cosmetic only. Skills must never read colour to learn a status.

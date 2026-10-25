# Tracker Sheet spec

The Google Sheet is the user's only dashboard and the source of truth. `/find-jobs` adds jobs to it, `/apply` updates it, and the user can edit it by hand or by telling Claude in chat. This file is the contract all of them follow.

Test Sheet built from this spec (dummy data only): https://docs.google.com/spreadsheets/d/1rABkXyB0sG4Dho64axx-gzngCgZf5_bakJimmQt7Hos/edit

## Where the skills find the Sheet

`profile.md` has a `## Tracker sheet` section that holds the full Sheet URL. Skills read that section and extract the spreadsheet ID, which is the part of the URL between `/d/` and the next `/`. Onboarding writes this section after it creates the Sheet.

## Tabs

### Jobs tab

One row per job. Row 1 is a bold, frozen header. Columns, in this order:

| Col | Name | Allowed values |
|---|---|---|
| A | Status | Dropdown, exactly these 8 in this order: New, Saved, Ready to apply, Applied, Interviewing, Offer, Passed, Closed |
| B | Fit | Whole number 1 to 10 |
| C | Type | Dropdown, exactly: Staff, Fellowship, Freelance, Nearby field |
| D | Job title | Text |
| E | Org | Text. Daybook jobs whose employer is paywalled carry `Employer hidden (Daybook)` here. The employer is only visible on the Daybook page, so he opens the link. `/apply` asks him to paste the details before building |
| F | Location | Text. Several locations are joined with a semicolon, for example `Washington, DC; Arlington, VA` |
| G | Why it fits | One or two plain sentences |
| H | Source | Text. One of: Indeed, JournalismJobs, Mediabistro, Daybook, LinkedIn alert, Pasted link |
| I | Posted | Date (yyyy-mm-dd) if known, blank otherwise. See "The 14-day window" |
| J | First seen | Date (yyyy-mm-dd) the scan first found the job |
| K | Link | URL of the posting |
| L | Folder | Link to the job's Google Drive folder (the résumé and cover letter Docs). Written by `/apply`. Blank until then |
| M | Notes | Free text. LinkedIn alert jobs carry "limited info" here (no description is available) |

The user's own extra columns must go to the right of Notes (column N onward). Skills never write to them.

Dropdowns, the Fit check, date formats and Status colours are applied to rows 2 to 1000, so new rows inherit them. Status colours are cosmetic only. Skills never read colour to learn a status.

### Scan log tab

One row per `/find-jobs` run. Row 1 is a bold, frozen header. Columns: Date | Sources checked | Sources that failed | New jobs added.

### Seen tab (hidden)

Claude's memory of every job found that cleared the Fit floor, whether or not it reached the Jobs tab. Hidden from the user, but the connector reads and writes it normally. Row 1 is a bold, frozen header. Columns, in this order:

| Col | Name | Notes |
|---|---|---|
| A | Link | URL |
| B | Source ID | The source's own job ID if it has one, else blank. When several jobs were collapsed into one (for example LinkedIn repeats), list every ID joined with `; `. Dedupe matches each one |
| C | Org | Text |
| D | Job title | Text |
| E | Location | Locations joined with a semicolon |
| F | Fit | 1 to 10 |
| G | First seen | Date (yyyy-mm-dd) |
| H | On Jobs tab | Dropdown: yes, no |
| I | Type | Same four values as the Jobs Type column. Stored so "show me more" can fill the Jobs row |
| J | Source | Same Source value as the Jobs tab |
| K | Posted | Date (yyyy-mm-dd) if known, blank otherwise |
| L | Why it fits | The one-line reason, stored for "show me more" |

## Status meanings and who sets them

| Status | Meaning | Set by |
|---|---|---|
| New | Found by a scan, not looked at yet | `/find-jobs` |
| Saved | He wants to keep it | User |
| Ready to apply | The application package is built and waiting for him | `/apply` |
| Applied | He submitted the application | User only (dropdown, or telling Claude in chat) |
| Interviewing | In a hiring process | User |
| Offer | He has an offer | User |
| Passed | He is not interested | User (dropdown, or chat) |
| Closed | The posting is gone or the process ended | User, or Claude when he says so in chat |

Skills never set Applied on their own.

## Reading the Sheet: by header, never by position

Every run, before anything else:

1. Read row 1 of the Jobs tab (and of Seen when needed).
2. Find the column index of each named column in this spec.
3. Build every range and every write from those indexes.
4. If a required header is missing or renamed, stop. Tell the user which header is missing and do not write anything.

Reads return displayed text, and trailing blank cells in a row are dropped, so a short row means the missing cells are blank. Always read fresh. Never rely on data from an earlier run or earlier in the conversation.

## Dedupe: how /find-jobs decides a job is already known

Dedupe reads **both** the Jobs tab and the Seen tab, in full, fresh.

### Normalise before comparing

- **Title:** lowercase, strip parenthetical text, strip the words Remote, Hybrid and Onsite, strip punctuation, collapse spaces.
- **Org:** lowercase, strip a leading "The", strip punctuation, collapse spaces.
- **Link:** strip tracking query strings like `utm_*` and a trailing slash.

### Collapse within one run first

LinkedIn repeats one role once per location. Before checking against the Sheet, collapse the found jobs on **Org + normalised Title**. Keep one job and join its locations into the Location value with semicolons, for example `Washington, DC; Arlington, VA`.

### A job is known if any of these match a row on Jobs or Seen

1. **Link or source ID.** Same normalised Link, or same Source ID. A Seen row may hold several IDs joined with `; `: match each one.
2. **Org + Title, with a location check.** Same normalised Org and normalised Title, and either the Locations overlap (any shared location), or either side is Remote, Hybrid, or DC metro. Some sources mint a new ID or link when they repost a job, which is why the first test alone is not enough.

### Daybook jobs with a hidden employer

When Org is `Employer hidden (Daybook)`, Org + Title cannot tell two different employers apart. For these rows, skip test 2 and use only test 1: the Daybook link, or the Daybook `_id` stored as Source ID. Do not collapse two hidden-employer jobs within a run on Org + Title either. Collapse only on the Daybook ID. Notes carries `Employer hidden, open the Daybook link`.

### Rules for known jobs

- Never add a known job again. No second row on Jobs, no second row on Seen.
- Never change the Status of a known job.
- **Repost:** a job that matches a Jobs or Seen row by the Org + Title rule, does **not** match it by Link or Source ID, and has the **same Source** as that row. When the matched row is on Jobs, append `Reposted YYYY-MM-DD, new link: <url>` to the end of that row's Notes, keeping existing text. Status Passed: do nothing. Status Closed: add the note and mention it in chat. Any other Status: add the note. A repost matched only to a Seen row: do nothing.
- **Cross-source copy:** the same Org + Title match where the new job's Source differs from the row's (for example the Mediabistro copy of a job already added from JournalismJobs). It's the same job seen on another board, not a repost. Do nothing: no row, no note, no mention in chat.
- Salary or other content changes are not tracked, because salary is not stored.

## The 14-day window

The window only filters sources that publish a date. For a source with no date, there is nothing to filter on, so the job is eligible, and its age counts from First seen on later runs.

- **Posted** is the date the source gives.
- Parse relative text such as "3 weeks ago" or "Posted 5 days ago" into a date when a source shows it.
- For LinkedIn alert emails, use the email's received date as Posted.
- If no date is available, leave Posted blank.

Age counts from Posted when known, otherwise from First seen. A job older than 14 days is not added to Jobs or Seen.

Seen rows that are **not** on the Jobs tab age out of the pool 14 days after their First seen. Aged-out rows are skipped when choosing jobs to promote, but they stay in Seen so dedupe still recognises them.

## Fit floor, the cap of 10, and the Seen tab

Per run:

1. Find, collapse and dedupe jobs, and keep only those with **Fit 6 or higher**. Jobs below 6 are dropped: not added to Jobs, not added to Seen.
2. Write **every** job that cleared the floor to Seen, with `On Jobs tab` set to `yes` or `no`.
3. Add only the **top 10 by Fit** to the Jobs tab, with `On Jobs tab` = `yes`. This is a hard cap. Ties break by newer Posted, then newer First seen.
4. The rest go to Seen with `On Jobs tab` = `no`. No `more` text is ever written in Notes.

The cap applies to this run's new jobs only: jobs that cleared the floor this run and aren't already known. Seen rows from earlier runs never compete for today's 10; they wait for "show me more". A run with 3 new jobs adds 3 even if 30 rows are waiting in Seen, and the "N more matched" line counts all waiting rows, old and new.

### "Show me more"

The chat summary of a run says: `N more matched below today's top 10. Say 'show me more' to add the next 10.` (N counts Seen rows with `On Jobs tab` = `no` that have not aged out.)

When the user says "show me more", Claude:

1. Reads Seen fresh and takes rows with `On Jobs tab` = `no`, skipping any whose First seen is more than 14 days ago.
2. Sorts them by Fit, highest first, and takes the next 10.
3. Appends those 10 to the Jobs tab as new rows (Status New, First seen from Seen, Folder blank, Notes blank), filling Type, Source, Posted and Why it fits from the Seen row and the rest from the stored job data, and sets their Seen rows to `On Jobs tab` = `yes`.
4. Does not write a Scan log row (no scan ran) and does not search sources again.
5. Tells him how many it added and how many are still waiting.

## What /find-jobs writes per run

Exactly this, and nothing else:

1. **Up to 10 new rows** on the Jobs tab (appended below the last row). Each has Status `New`, Fit, Type, Job title, Org, Location, Why it fits, Source, Posted (blank if unknown), First seen (today), Link, Folder blank, Notes (`limited info` for LinkedIn alert jobs, otherwise blank).
2. **Seen rows** for every found job that cleared the Fit floor (see above).
3. **Notes additions** on existing Jobs rows only for the repost case.
4. **One Scan log row**: Date (today), Sources checked, Sources that failed (blank if none), New jobs added (rows added to Jobs this run, 0 to 10).

It never edits Status, Fit, Type, Folder or any other cell of an existing Jobs row, and never deletes or reorders rows.

### Chat summary

Each run ends with a short summary in chat that includes:

- Found: the number of distinct jobs that passed the relevance filter, the 14-day window and the Fit floor, after copies of the same job within this run were collapsed, and before the Sheet check. It shows as "found N that fit" in chat. It is a number in the chat only, not a column.
- New jobs added to the Jobs tab.
- The line `N more matched below today's top 10. Say 'show me more' to add the next 10.` when N is above 0.
- Sources that failed.
- A one-line nudge for each row whose Status is still **Ready to apply**, for example `Still waiting on you: Politics editor at Sample Gazette is Ready to apply. Say "I applied" when you submit it.`
- Any Closed-row repost, as described above.

## What /apply writes

Only these cells on the one row for the job being applied to:

- **Folder** (column L): the link to the job's Google Drive folder. Always written. If the Drive connector is off, the local folder path instead.
- **Status** (column A): set to `Ready to apply`, but **only when the current Status is New or Saved**. For any other Status (for example Applied, Interviewing, Passed), leave Status alone, still write Folder, and say so in the reply.
- **Notes** (column M): append `Apply by yyyy-mm-dd` when the posting gives a deadline, keeping existing text.

`/apply` never sets Applied. Find the row by Link, or by the Org + Title rule, and write only those cells, not the whole row.

## The user changes status in chat

A request like "I applied to the Axios one" or "pass on Mock Policy Group" is handled by Claude:

1. Read the Jobs tab fresh and match the row by Org, Title or Source words from the request.
2. If more than one row matches, ask which one. Do not guess.
3. Write only the Status cell. **Any of the 8 values is allowed, with no transition limits** (the user may move a job backwards or sideways).
4. Read the row back and confirm to the user what the row now says.

## The user edits the Sheet too

The user can change Status from the dropdown and edit Notes, Fit or anything else at any time. Therefore:

- Every run reads fresh and never overwrites a Status the user set.
- Existing rows are changed one cell at a time, never rewritten.
- If a skill wants a Status change that the rules above do not allow, it leaves the cell and says so in the reply.

## How onboarding creates his Sheet

**Recommendation: (a) Claude creates it from scratch through the connector.**

It worked end to end in the test, needs no template link to host or keep shared, and the Sheet lands in his own Drive owned by him. Option (b) needs a public or shared template that can break or be edited by others, and adds a manual "Make a copy" step for a beginner.

Prerequisite: the Google Drive and Google Sheets connectors must both be on. Drive creates the file, Sheets edits it. If Sheets is off, onboarding stops and asks him to enable it. After creating the Sheet, onboarding writes the `## Tracker sheet` section in `profile.md` with the full URL.

## Connector calls that worked

Tool names are for the Claude connectors used in the test. The long prefix differs per install, so match on the suffix.

### 1. Create the empty Sheet (Drive connector)

`create_file` with `title` = `Job tracker` (the test used `TEST - Nihal job tracker (dummy data)`) and `contentMimeType` = `application/vnd.google-apps.spreadsheet`. No content. Returns `id` and `viewUrl`. The new Sheet has one tab, `sheetId` 0, named `Sheet1`.

### 2. Tabs and frozen headers (Sheets connector)

`update_spreadsheet` with `spreadsheetId` and `requests`:

- `updateSheetProperties` on sheetId 0: `properties` `{sheetId:0, title:"Jobs", gridProperties:{frozenRowCount:1}}`, `fields` `"title,gridProperties.frozenRowCount"`.
- `addSheet` with `properties` `{sheetId:1, title:"Scan log", gridProperties:{rowCount:500, columnCount:4, frozenRowCount:1}}`.
- `addSheet` with `properties` `{sheetId:2, title:"Seen", gridProperties:{rowCount:2000, columnCount:12, frozenRowCount:1}}`.

### 3. Headers and data (Sheets connector)

`update_values` with `range` `Jobs!A1:M9` and `values` as a 2D array. The range must be **at least as large** as the data: a range smaller than the data fails (A1:M8 with 9 rows errored with "tried writing to row [9]"), while a larger range works (Seen!A1:L5 with 4 rows was fine). Quote tab names that have spaces: `'Scan log'!A1:D3`.

### 4. Formatting, dropdowns, colours (one `update_spreadsheet` call)

All worked in one batch:

- Header: `repeatCell` on row 0, `fields` `"userEnteredFormat.textFormat.bold,userEnteredFormat.backgroundColor,userEnteredFormat.verticalAlignment"`.
- Column widths: `updateDimensionProperties`, `dimension` `COLUMNS`, `properties.pixelSize`, `fields` `"pixelSize"`. Used: Status 100, Fit 50, Type 110, Job title 230, Org 180, Location 130, Why it fits 320, Source 130, Posted 90, First seen 90, Link 260, Folder 260, Notes 220.
- Wrap text on Why it fits: `userEnteredFormat.wrapStrategy` `WRAP`.
- Date format on Posted, First seen, Scan log Date, Seen First seen and Seen Posted: `numberFormat` `{type:"DATE", pattern:"yyyy-mm-dd"}`.
- Status dropdown: `setDataValidation`, column A with `startRowIndex` 1, `endRowIndex` 1000 (rows 2 to 1000), rule `{condition:{type:"ONE_OF_LIST", values:[{userEnteredValue:"New"}, ...8 values]}, strict:true, showCustomUi:true}`.
- Type dropdown: same, column C of Jobs and column I of Seen.
- Fit check: `setDataValidation` on column B with `NUMBER_BETWEEN` `1` and `10`, `strict:true`.
- Status colours: one `addConditionalFormatRule` per status, `condition.type` `TEXT_EQ`, same column A range, `format.backgroundColor`. Colours: New light blue, Saved light yellow, Ready to apply light orange, Applied light green, Interviewing light purple, Offer stronger green, Passed and Closed grey.
- To change the Status list later: `deleteConditionalFormatRule` (`{sheetId, index:0}`, repeated once per rule), re-run `setDataValidation`, then `addConditionalFormatRule` again.

### 5. Hide the Seen tab (Sheets connector)

`update_spreadsheet` with `updateSheetProperties` `{properties:{sheetId:2, hidden:true}, fields:"hidden"}`. This worked, and `get_values` and `update_values` still read and write the hidden tab normally. The user can unhide it from the Sheet's tab list if he is curious.

### 6. Read (Sheets connector)

`get_values` with `range` `Jobs!A1:M1000` (or `Seen!A1:L2000`). Returns displayed strings, so Fit is `"9"` and dates are `"2026-10-20"`. Trailing blanks are dropped, so rows are ragged. Map by header, treat missing cells as blank.

### 7. Append rows (Sheets connector)

`append_values` with `range` `Jobs!A1` and `values` `[[...13 cells...]]`. It writes to the next empty row. Send all 13 cells, `""` for blank ones. Seen and Scan log work the same: `Seen!A1` (send all 12 cells), `'Scan log'!A1`. Several rows can go in one call.

### 8. Change one cell (Sheets connector)

`update_values` with one cell, for example `range` `Jobs!A3`, `values` `[["Ready to apply"]]`, then `range` `Jobs!L3` for Folder. Find the row number from a fresh read (array index plus 1 for the header). Use the column letter found from the header, not a fixed letter.

### 9. Verify (Sheets connector)

`get_values` on the changed range. To check dropdowns or date types: `get_spreadsheet` with `includeGridData:true`, `ranges`, and `fields` such as `sheets.data.rowData.values.dataValidation` (full paths, no parentheses). `get_spreadsheet` with `fields` `["sheets.properties"]` shows `hidden` and the row count.

## Things that were awkward

- **Appended rows inherit dropdowns and colours** because they were applied to rows 2 to 1000 up front. After row 1000 they stop. If the Jobs tab ever passes about 1000 rows, extend the ranges or archive old rows.
- **Write calls parse like the Sheets UI.** `"2026-10-20"` becomes a real date and `"9"` a number, which is what we want. Text that looks like a number, date or formula (a title like `1/2 time editor`) needs a leading apostrophe.
- **Reads drop trailing blanks**, see call 6.
- **Link column** holds plain URLs. Sheets makes them clickable by itself.

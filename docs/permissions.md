# Permissions for /find-jobs

This is a reference list of what the morning scan needs. It is not a settings file, and this repo deliberately ships no allowlist. Reasons:

- Whether the Claude desktop app's scheduled tasks honour a project's `.claude/settings.json` is not documented, and project rules need folder trust.
- The Gmail and Google Sheets connector names contain an ID that is different on every install, and MCP allow rules cannot wildcard that part. A repo cannot ship rules that work.

So setup is one supervised "Run now" where Nihal clicks "Always allow" on each prompt (see `docs/morning-scan.md`). The approvals are stored on that task. Use this page to know what to expect, to recognise a prompt, or to write your own rules.

Docs: https://code.claude.com/docs/en/permissions.md and https://code.claude.com/docs/en/desktop-scheduled-tasks.md

## How rules match (facts that matter here)

- The current Bash form is space-star: `Bash(curl -s https://host/path *)`. The older `:*` form is equivalent but only valid at the end.
- Rules match the command text as written. Quoting is not normalized. A single-quoted URL is a different string from an unquoted one.
- A space-star rule does not match a query string attached with no space, for example `...fiscalnote?mode=json`.
- Compound commands are split on `&&`, `||`, `;`, `|`, `|&`, `&` and newlines. Every part must match its own rule. So the filters after a pipe (`jq`, `grep`, `sed`, `tr`) need rules, and so does `wait` (not documented, so it may prompt).
- WebFetch rules look like `WebFetch(domain:host)`.
- MCP allow rules are `mcp__<server>`, `mcp__<server>__*` or `mcp__<server>__<tool>`. The server part cannot be wildcarded.
- A run in Manual (ask) mode stalls at each prompt until someone answers. Choosing "Always allow" stores the approval on that task. Approvals can be revoked in the task's "Always allowed" panel. MCP tools marked requiresUserInteraction prompt every time.

## 1. Bash (curl and filters)

The skill runs `curl` on the network and pipes the output through `jq`, `grep`, `sed` and `tr`. It runs several curls at once with `&` and `wait`.

| Host | Used for | Method |
|---|---|---|
| boards-api.greenhouse.io | Axios, Semafor, NPR job lists and single jobs | GET |
| api.lever.co | FiscalNote (CQ Roll Call) | GET |
| washpost.wd5.myworkdayjobs.com | Washington Post list and detail | POST (list), GET (detail) |
| american.wd1.myworkdayjobs.com | WAMU list and detail | POST (list), GET (detail) |
| www.journalismjobs.com | DC listing and per-job pages for dates | GET |
| 19thnews.org | The 19th careers page | GET |

Example rules in space-star form. They match only if the command is written exactly this way, including the quotes around the user agent:

```
Bash(curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/ *)
Bash(curl -s -A 'Mozilla/5.0' -m 30 https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/ *)
Bash(curl -s -A 'Mozilla/5.0' -m 30 https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/ *)
Bash(curl -s -A 'Mozilla/5.0' -m 30 https://19thnews.org/careers/ *)
Bash(jq *)
Bash(grep *)
Bash(sed *)
Bash(tr *)
Bash(command -v jq)
Bash(wait)
```

Two sources will not match a rule like the ones above as the skill writes them today:
- FiscalNote: `'https://api.lever.co/v0/postings/fiscalnote?mode=json'` is single-quoted and has a query string with no space.
- JournalismJobs: `'https://www.journalismjobs.com/job-listings?location=7'` is single-quoted and has a query string. The per-job page fetches (`https://www.journalismjobs.com/{path}`) are fine.

That is why the supervised "Always allow" run is the setup method: the approval is stored for the exact command the first run used. Other notes:
- `jq` is optional. If `command -v jq` fails the skill uses grep instead.
- No Python, no file writes and no shell scripts are needed.

## 2. WebFetch

Pages where curl is blocked or the page is too messy to read raw:

```
WebFetch(domain:punchbowl.news)
WebFetch(domain:thedispatch.com)
WebFetch(domain:www.mediabistro.com)
WebFetch(domain:mediajobboard.com)
```

Pasted job links can be on any site, so an unattended run cannot allow them in advance. The skill skips (and reports) a pasted link it is not allowed to open. The morning task has no pasted links anyway.

## 3. Gmail connector (LinkedIn job alerts)

Read only. The skill searches for `from:jobalerts-noreply@linkedin.com newer_than:3d` and reads the matching threads. It never labels, drafts, sends or deletes.

```
mcp__<gmail-server-id>__search_threads
mcp__<gmail-server-id>__get_thread
```

## 4. Google Sheets connector

Read and write, on his tracker Sheet only.

```
mcp__<sheets-server-id>__get_values       reads Jobs, Seen, Scan log
mcp__<sheets-server-id>__append_values    adds rows to Jobs, Seen, Scan log
mcp__<sheets-server-id>__update_values    one-cell edits: repost Notes, Seen "On Jobs tab", Status changes he asks for
mcp__<sheets-server-id>__get_spreadsheet  optional, only to check tab names or hidden state
```

The `<server-id>` is a long ID that is different on each install. Do not copy these lines into a settings file as they are.

Not needed by `/find-jobs`: `update_spreadsheet`, `batch_clear_values`, Drive tools. Onboarding uses those. Do not click "Always allow" on them for the morning task.

## 5. Built-in tools

```
ToolSearch     to load the Sheets and Gmail tools, which are deferred
Read           profile.md and learnings.md in the repo
```

Reading files inside the working folder is normally allowed. The skill writes `learnings.md` only after he says yes, which cannot happen in an unattended run, so no Write or Edit permission is needed for the morning scan.

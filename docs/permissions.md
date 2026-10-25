# Permissions for /find-jobs

This is a reference list of what the morning scan needs. It is not a settings file, and this repo deliberately ships no allowlist. Reasons:

- Whether the Claude desktop app's scheduled tasks honour a project's `.claude/settings.json` is not documented, and project rules need folder trust.
- The Gmail and Google Sheets connector names contain an ID that is different on every install, and MCP allow rules cannot wildcard that part. A repo cannot ship rules that work.

So the morning task runs in **Auto** permission mode (see `docs/morning-scan.md`). Tested 2026-10-25: in Manual mode, "Always allow" did not carry over between runs for these commands, and every curl line asked again. In Auto mode, a full run finished with no prompts. Use this page to know what the scan touches, to recognise a prompt, or to write your own rules.

Docs: https://code.claude.com/docs/en/permissions.md and https://code.claude.com/docs/en/desktop-scheduled-tasks.md

## How rules match (facts that matter here)

- Rules match the command text as written. Quoting is not normalized. A single-quoted URL is a different string from an unquoted one.
- Compound commands are split on `&&`, `||`, `;`, `|`, `|&`, `&` and newlines. Every part must match its own rule. So each filter after a pipe (`jq`, `grep`, `sed`, `tr`) is checked too, and `wait` is not documented, so the skill never uses it.
- WebFetch is approved per domain.
- MCP tools are approved per tool. The server part of the name cannot be wildcarded.
- "Always allow" saves the exact command text or domain that was used. Any change between runs (a new domain, different quoting, a different filter, a different offset) prompts again. That is why the skill's unattended mode uses fixed text and never varies it.
- A run in Manual (ask) mode stalls at each prompt until someone answers. Approvals can be revoked in the task's "Always allowed" panel. MCP tools marked requiresUserInteraction prompt every time.

## Unattended mode: tools used

This list matches the "Unattended mode: tools used" section of `.claude/skills/find-jobs/SKILL.md`. If one changes, change the other.

- Bash: the 11 fixed `curl` lines below. Nothing else.
- WebFetch: 4 domains (`punchbowl.news`, `thedispatch.com`, `www.mediabistro.com`, `mediajobboard.com`). Nothing else.
- ToolSearch: two fixed queries, `get_values append_values update_values` and `search_threads get_thread`.
- Read: `profile.md` and `learnings.md`.
- Gmail: `search_threads` and `get_thread`.
- Google Sheets: `get_values`, `append_values`, `update_values`. Never `get_spreadsheet`.

## 1. Bash (curl and filters)

The skill runs these 11 lines one at a time, copied character for character. No `&`, no `wait`, no `;`, no detail-page fetches, no `command -v jq`. `jq` must be installed. On this Mac it is at `/usr/bin/jq`.

```
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/axios/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/semafor/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/nationalpublicradioinc/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://api.lever.co/v0/postings/fiscalnote | jq -c '.[] | {id, title: .text, loc: .categories.location, posted: (.createdAt/1000|strftime("%Y-%m-%d")), url: .hostedUrl}'
curl -s -A 'Mozilla/5.0' -m 30 https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":0,"searchText":""}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":20,"searchText":""}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":0,"searchText":"WAMU"}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":20,"searchText":"WAMU"}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.journalismjobs.com/job-listings?location=7' | grep -o 'href="/[0-9]\{6,\}-[^"]*"\|Posted [A-Z][a-z]* [0-9]*\|Featured Jobs'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.journalismjobs.com/job-listings?location=7&jobType=4' | grep -o 'href="/[0-9]\{6,\}-[^"]*"\|Posted [A-Z][a-z]* [0-9]*\|Featured Jobs'
curl -s -A 'Mozilla/5.0' -m 30 https://19thnews.org/careers/ | sed 's/<[^>]*>/ /g' | tr -s ' \n\t' ' '
```

Line by line: 1 Axios, 2 Semafor, 3 NPR, 4 CQ Roll Call (FiscalNote), 5 and 6 Washington Post pages 1 and 2, 7 and 8 WAMU pages 1 and 2, 9 JournalismJobs DC, 10 JournalismJobs DC freelance, 11 The 19th. Lines 6 and 8 run only when page 1 shows more recent jobs may be waiting. The skill never runs a third page.

Hosts used: boards-api.greenhouse.io, api.lever.co, washpost.wd5.myworkdayjobs.com, american.wd1.myworkdayjobs.com, www.journalismjobs.com, 19thnews.org.

Keeping these lines fixed still matters: it keeps the scan predictable and easy for Auto mode's safety check to approve.

## 2. WebFetch

```
WebFetch(domain:punchbowl.news)
WebFetch(domain:thedispatch.com)
WebFetch(domain:www.mediabistro.com)
WebFetch(domain:mediajobboard.com)
```

Unattended, the skill fetches only these four pages and never opens a job's own page or apply link. Where details would need another site it keeps what the list gives and writes "details not checked" in Notes. Pasted links work in attended runs only, where you can approve a new site.

## 3. Gmail connector (LinkedIn job alerts)

Read only. The skill makes one `search_threads` call with `query` `from:jobalerts-noreply@linkedin.com newer_than:3d`, `pageSize` 20, `view` `THREAD_VIEW_MINIMAL`. Then it calls `get_thread` with `messageFormat` `PLAIN_TEXT` for each thread. It never labels, drafts, sends or deletes.

```
mcp__<gmail-server-id>__search_threads
mcp__<gmail-server-id>__get_thread
```

## 4. Google Sheets connector

Read and write, on the tracker Sheet only.

```
mcp__<sheets-server-id>__get_values       reads Jobs, Seen, Scan log
mcp__<sheets-server-id>__append_values    adds rows to Jobs, Seen, Scan log
mcp__<sheets-server-id>__update_values    one-cell edits: repost Notes, Seen "On Jobs tab", Status changes he asks for
```

The `<server-id>` is a long ID that is different on each install. Do not copy these lines into a settings file as they are.

Not used by the morning scan: `get_spreadsheet`, `update_spreadsheet`, `batch_clear_values`, Drive tools. Onboarding uses some of those. Do not click "Always allow" on them for the morning task.

## 5. Built-in tools

```
ToolSearch     to load the Sheets and Gmail tools, which are deferred
Read           profile.md and learnings.md in the repo
```

Reading files inside the working folder is normally allowed. The skill writes `learnings.md` only after he says yes, which cannot happen in an unattended run, so no Write or Edit permission is needed for the morning scan.

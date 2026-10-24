# Permissions for /find-jobs

The morning scan (NAD-36) runs `/find-jobs` with nobody there to click "Allow". Anything not on an allowlist stops the run. This page lists every permission the skill uses. It changes no settings. NAD-36 turns it into rules.

Rule syntax below is Claude Code's (`Bash(...)`, `WebFetch(domain:...)`, MCP tool names). Check it against the current Claude Code permissions docs when you write the settings file.

## 1. Bash (curl only)

The skill runs `curl` and nothing else on the network. It also pipes curl output through `sed`, `tr`, `grep` and `jq`, and runs several curls at once with `&` and `wait`.

Hosts curl talks to:

| Host | Used for | Method |
|---|---|---|
| boards-api.greenhouse.io | Axios, Semafor, NPR job lists and single jobs | GET |
| api.lever.co | FiscalNote (CQ Roll Call) | GET |
| washpost.wd5.myworkdayjobs.com | Washington Post list and detail | POST (list), GET (detail) |
| american.wd1.myworkdayjobs.com | WAMU list and detail | POST (list), GET (detail) |
| www.journalismjobs.com | DC listing and per-job pages for dates | GET |
| 19thnews.org | The 19th careers page | GET |

Allow rules (a prefix rule cannot restrict the host, so this is broad on purpose):

```
Bash(curl:*)
Bash(jq:*)
Bash(sed:*)
Bash(tr:*)
Bash(grep:*)
Bash(wait)
```

Notes for NAD-36:
- Claude Code checks each command of a pipe or `&` list separately, so every piece has to be allowed.
- If a tighter rule is wanted, the skill's curl calls all start with `curl -s -A 'Mozilla/5.0' -m 30`. Rules matching that prefix cover them all. The two POSTs add `-X POST`.
- `jq` is optional. If it is not installed the skill reads the raw JSON instead.
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

The server id is different on each install (a long UUID in the tool name). Match on the tool name after the last `__`, or read the real prefix from the tool list.

Not needed by `/find-jobs`: `update_spreadsheet`, `batch_clear_values`, Drive tools. Onboarding uses those. Leave them off the morning allowlist.

## 5. Built-in tools

```
ToolSearch     to load the Sheets and Gmail tools, which are deferred
Read           profile.md and learnings.md in the repo
```

`Read` of profile.md and learnings.md is normally allowed inside the project folder already. The skill writes `learnings.md` only after he says yes, which cannot happen in an unattended run, so no Write or Edit permission is needed for the morning scan.

## Quick check before turning the morning scan on

Run `/find-jobs` once by hand and watch for any permission prompt. Each prompt that appears is a missing rule.

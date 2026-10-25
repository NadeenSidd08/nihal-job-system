# Permissions for /find-jobs

This is a reference list of what the morning scan needs. It is not a settings file, and this repo deliberately ships no allowlist. Reasons:

- Whether the Claude desktop app's scheduled tasks honour a project's `.claude/settings.json` is not documented, and project rules need folder trust.
- The Gmail, Google Sheets and Indeed connector names contain an ID that is different on every install, and MCP allow rules cannot wildcard that part. A repo cannot ship rules that work.

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

- Bash: the 12 fixed `curl` lines below. Nothing else.
- WebFetch: 1 domain (`www.mediabistro.com`). Nothing else.
- ToolSearch: three fixed queries, `get_values append_values update_values`, `search_threads get_thread` and `Indeed job search`.
- Read: `profile.md` and `learnings.md`.
- Indeed connector: `search_jobs` and `get_job_details`.
- Gmail: `search_threads` and `get_thread`.
- Google Sheets: `get_values`, `append_values`, `update_values`. Never `get_spreadsheet`.

## 1. Bash (curl and filters)

The skill runs these 12 lines one at a time, copied character for character. No `&`, no `wait`, no `;`, no detail-page fetches, no `command -v jq`. `jq` must be installed (Daybook lines). On this Mac it is at `/usr/bin/jq`.

```
curl -s -A 'Mozilla/5.0' -m 30 'https://www.journalismjobs.com/job-listings?location=7' | grep -o 'href="/[0-9]\{6,\}-[^"]*"\|Posted [A-Z][a-z]* [0-9]*\|Featured Jobs'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.journalismjobs.com/job-listings?location=7&jobType=4' | grep -o 'href="/[0-9]\{6,\}-[^"]*"\|Posted [A-Z][a-z]* [0-9]*\|Featured Jobs'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/in/washington-dc?q=reporter' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/in/washington-dc?q=editor' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/in/washington-dc?q=journalist' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/in/washington-dc?q=writer' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/remote?q=reporter' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/remote?q=editor' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/remote?q=journalist' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/remote?q=writer' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/communications/in/washington-dc' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
curl -s -A 'Mozilla/5.0' -m 30 'https://www.daybook.com/jobs/public-policy/in/washington-dc' | grep -o '__APOLLO_STATE__={.*}</script>' | sed 's/^__APOLLO_STATE__=//; s/<\/script>$//' | jq -c '. as $d | .ROOT_QUERY | to_entries[] | select(.key | startswith("getJobs")) | .value.jobs[].__ref as $r | $d[$r] | {id: ._id, title, org: .organization, paywalled: .organizationPaywalled, loc: .location.name, posted: .dateAdded[:10], url: .slugUrl}'
```

Line by line: 1 JournalismJobs DC, 2 JournalismJobs DC freelance, 3 to 6 Daybook Washington DC (reporter, editor, journalist, writer), 7 to 10 Daybook remote (the same four terms), 11 and 12 Daybook Communications and Public Policy category pages for DC. Line 2 runs only if Freelance is a ticked opportunity type. Lines 11 and 12 run only if Nearby field is ticked.

Hosts used: www.journalismjobs.com, www.daybook.com.

Keeping these lines fixed still matters: it keeps the scan predictable and easy for Auto mode's safety check to approve.

## 2. WebFetch

```
WebFetch(domain:www.mediabistro.com)
```

Unattended, the skill fetches only this page and never opens a job's own page or apply link. Where details would need another site it keeps what the list gives and writes "details not checked" in Notes. Pasted links work in attended runs only, where you can approve a new site.

## 2b. Indeed connector

Read only. The skill makes one `search_jobs` call per target role in `profile.md`, for the location `Washington, DC` and again for `remote`, with `country_code` `US` and no `job_type`. A profile with 3 roles means 6 calls. It calls `get_job_details` only for a job that already passed the relevance filter and the Fit floor. It does not save or apply to anything on Indeed.

```
mcp__<indeed-server-id>__search_jobs
mcp__<indeed-server-id>__get_job_details
```

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
ToolSearch     to load the Sheets, Gmail and Indeed tools, which are deferred
Read           profile.md and learnings.md in the repo
```

Reading files inside the working folder is normally allowed. The skill writes `learnings.md` only after he says yes, which cannot happen in an unattended run, so no Write or Edit permission is needed for the morning scan.

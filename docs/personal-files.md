# Personal files: the shared contract

Every skill in this repo reads and writes the same small set of personal files. This page is the contract between them. Onboarding creates these files. `/find-jobs`, `/apply`, and `/jobs` read them. All of them are gitignored and never committed.

## Where things live

```
his-folder/                      his working folder (he opens Claude in `nihal-job-system` inside it)
  résumé (PDF or .docx)          his original, the formatting source of truth
  LinkedIn profile PDF           saved from LinkedIn ("Save to PDF")
  resume.md                      faithful markdown copy of the résumé (created in onboarding)
  nihal-job-system/              this repo
    profile.md                   his search settings (below)
    facts.md                     light fact list (below)
    clips.md                     clips library (below)
    clips/                       optional full text of clips, one file per clip, gitignored
    learnings.md                 what Claude has learned, each line approved by him (below)
    feedback.md                  notes for Nadeen (built in NAD-42)
    applications/<Org - Role>/   one folder per job /apply builds
```

The Google Sheet (the Jobs, Scan log, and hidden Seen tabs) lives in his Google Drive. Its rules are in `docs/tracker-sheet.md`.

## profile.md

Keep every `##` heading exactly as written. Skills find sections by heading name.

```markdown
# Job search profile: <first name>

## Background
2 to 4 sentences from resume.md, the LinkedIn PDF, and clips.md: current role, beats, outlets, standout skills.

## Beats and expertise
- <beat or subject area>, one per line, strongest first

## Target roles
- <role title>, 2 to 5 lines, for example "Congressional reporter", "Politics editor"
Excluded: <titles he never wants to see, or "none">

## Opportunity types
In priority order. Only the types he wants:
1. Staff
2. Fellowship
3. Freelance
4. Nearby field (for example comms, policy writing, think tank editing)

## Location
<for example "Washington, DC metro or remote">

## Salary floor
<for example "$70,000", or "none">

## Dealbreakers
- <one per line, or "none">

## Sources
The sources /find-jobs checks. On by default, he can turn any off.
- [x] Indeed
- [x] JournalismJobs (DC)
- [x] Mediabistro (national)
- [x] Daybook
- [x] LinkedIn job alerts (Gmail)

## Tracker sheet
<full Google Sheet URL>

## Applications folder
(set on first /apply)

## Scoring rubric
Fit = role match (0 to 4) + beat and substance (0 to 4) + logistics (0 to 2). Floor: 6.
### Role match (0 to 4)
<anchors written for his target roles>
### Beat and substance (0 to 4)
<anchors written against his beats and clips>
### Logistics (0 to 2)
<anchors for location, salary, and type>
### Calibration anchors
<empty at onboarding. After the first scans, 2 to 4 real examples like "Org Role: 4+3+2 = 9">

## Morning scan
<"On, weekdays at 7:30" or "Off">
```

## facts.md (light fact list)

Built in onboarding from resume.md, the LinkedIn PDF, and clips.md. There's no word-for-word review and no Python. He skims it once. Every line names its source, so `/apply` only uses claims that came from his own material or from him.

```markdown
# Facts

## Roles
- <Title>, <Outlet>, <start> to <end> (source: résumé)

## Outlets and bylines
- <Outlet> (source: clips)

## Awards, fellowships, education
- <item> (source: LinkedIn)

## Numbers and results
- <for example "Broke 3 stories picked up by national outlets"> (source: he said, 2026-10-24)

## Skills and other
- <for example "Reports in Spanish"> (source: he said, 2026-10-24)
```

Allowed sources: `résumé`, `LinkedIn`, `clips`, `he said, <date>`. Anything he says in chat that isn't on his materials is added only after he confirms it, tagged `he said`.

## clips.md (clips library)

Built in onboarding by reading his website once. He reviews it, edits or removes rows, and stars his favorites. "I published something new" adds a row.

```markdown
# Clips

Status: approved <date>   (or "draft, not yet reviewed")

| Star | Headline | Outlet | Date | Beat | Link | One-line summary |
|---|---|---|---|---|---|---|
| * | <headline> | <outlet> | 2026-05-02 | <beat> | <url> | <one line> |
```

Optional: `clips/` holds the full text of clips, one `.md` or `.txt` file per clip, named by slug. It is gitignored. `/apply` reads a clip from here first and fetches the link only if the file is missing.

`/apply` picks 3 to 5 clips per job from this table, starred clips first when they fit.

## learnings.md

The in-the-moment learning loop. Claude shows the exact line and asks "Want me to remember that?", he approves it, and Claude writes it. "Forget that" removes it, and if that empties a section Claude puts `- (none yet)` back. Claude learns preferences only, never facts about his experience. Those go in facts.md, and only with his confirmation.

```markdown
# What Claude has learned

## Job matching
- 2026-10-24: Skip roles that are mainly social media or newsletter production (he passed on two, "not reporting")

## Writing voice
- 2026-10-24: Never use "passionate" or "thrilled"

## Clips
- 2026-10-24: Lead with the Capitol coverage clips for politics roles

## Sources
- (none yet)
```

Every skill reads learnings.md at the start and applies it. When he passes on a job in chat, Claude asks for a one-line reason, writes it to that row's Notes in the Sheet, and if it sounds like a pattern, offers a learnings line.

## Status changes in chat

"I applied to the Bulwark one" or "pass on that Indeed job" changes the Status cell on the matching Sheet row (see `docs/tracker-sheet.md`). If the request matches more than one row, ask which one. For Passed, ask for a one-line reason.

---
name: jobs
description: The front-door menu for the job search system. Shows where his Sheet stands and a short lettered list of what he can do next, then runs the pick. Use when he types "/jobs", or says "what can you do", "help with my job search", "menu", "what should I do next", "I forgot the commands", or "I'm not sure what to do".
---

# /jobs

The menu. He opens this when he is not sure what to do or forgot the commands. It must fit on one screen, in plain words. It does not do the work itself. It shows the state, offers the choices, and hands the pick to the skill or rule that already handles it.

Rules that never bend:
- Never invent a count. Every number comes from a fresh read of the Sheet in this run.
- Plain language, short sentences. No em dashes, no emojis.
- Do not re-implement any flow here. Name the skill or rule and follow it.
- No long explanations unless he asks "what does C do?". Then answer in two or three lines.

## Step 1: check the setup (the onboarding gate)

Before showing anything, check the three conditions in `CLAUDE.md` under "Onboarding gate":

- `profile.md` does not exist.
- `profile.md` still contains `⟨`, or `## Tracker sheet` says `(not set up yet)`, or `## Morning scan` says `(not decided yet)`.
- `learnings.md` does not exist.

If any is true, do not show the menu. Say in one or two sentences that setup is not finished and name what is missing, then offer: "Want to keep going with setup? It picks up where you stopped." On yes, read `docs/onboarding.md` and follow it from the first unfinished step. Stop there.

## Step 2: read the Sheet fresh

Read `profile.md` and `learnings.md` (apply them). Take the Sheet URL from `profile.md`, `## Tracker sheet`, and get the spreadsheet ID (between `/d/` and the next `/`). Read per `docs/tracker-sheet.md`: `get_values` on `Jobs!A1:Z1000` and `Seen!A1:L2000`, find columns by header name (never position), rows are ragged so a short row means blank trailing cells. Do not write anything in this step.

Count from the Jobs tab, by the Status column:
- Total rows (rows with a Job title or Org).
- New, Saved, Ready to apply, Applied, Interviewing, Offer. Passed and Closed are counted together.

Count from the Seen tab: rows with `On Jobs tab` = `no` whose First seen is not more than 14 days before today. That is "waiting" (the same count `/find-jobs` uses for "show me more").

If a required header is missing, say which one and skip the counts. If the Sheet cannot be read at all (connector off, bad URL, no access), say so in one plain line, skip the status line, and still show the menu. If only Seen cannot be read, leave out the "waiting" part and the "Show me more" count.

## Step 3: show the status line, then the menu

One status line first. List only the statuses that are above zero, in this order: new, saved, ready to apply, applied, interviewing, offer, passed or closed. Then the waiting count if above zero. Examples:

`12 jobs on your Sheet: 4 new, 2 ready to apply, 1 applied, 3 passed or closed. 2 more waiting.`

The counts must add up to the total.

End the status block with a line `Sheet: <the full URL from profile.md>`.

An empty Sheet: `Your Sheet has no jobs yet.`

Then a one-line recommendation, chosen from the state (first rule that fits):

1. Any Ready to apply: `Suggested: D, tell me which ones you've sent so I can mark them Applied. Or C, to finish applying.` Name the job if there is just one.
2. Many New (5 or more): `Suggested: look over the New rows on your Sheet, or pick the best one to apply to (C).`
3. Some New (1 to 4): `Suggested: look over the New rows, then pick one to apply to (C).`
4. Nothing New and no Ready to apply, but jobs are waiting: `Suggested: B, to bring in the next 10 waiting.`
5. Sheet could not be read: `Suggested: I, if a scan got stuck. Otherwise A, to look for new jobs.`
6. Nothing at all to act on: `Suggested: A, to look for new jobs.`

Then the menu, exactly this text. For B, use the waiting count N: 10 or more, `Show me more (10 of N waiting)`. Fewer than 10 but more than 1, `Show me more (all N waiting)`. Exactly 1, `Show me more (1 waiting)`. Zero, `Show me more (none waiting)`. If Seen could not be read, `Show me more (the next 10 waiting)`. Put the real number in place of N.

```
What do you want to do?

A  Find new jobs now
B  Show me more (10 of N waiting)
C  Apply to a job (pick one, paste a link, or "my top 3 this week")
D  Update a job's status ("I applied to...", "pass on...")
E  Add a new clip ("I published something new")
F  Change my preferences (salary floor, roles, sources, location)
G  See what Claude has learned about me, and change it
H  Update my job system
I  Help: how the morning scan works, or a scan that got stuck

Pick a letter, or just say it in your own words.
```

Note: Add Weekly check-in and Tell Nadeen here when NAD-42 ships.

Show the recommendation line between the status line and the menu. Total length stays under about 20 lines.

## Step 4: route the pick

He may type a letter or a plain sentence ("I applied to the Bulwark one", "what did you learn about me"). Match it to a row below. Do not ask him to choose a letter if the sentence is clear. If it fits two, ask which in one line.

| Pick | Do this |
|---|---|
| A | Run `/find-jobs` (a full scan). |
| B | Run `/find-jobs` in its "Show me more" mode: no source search, no Scan log row. Say what it will add (how many, from the waiting list) and then do it. |
| C | Run `/apply`. If he gave a job, a link or "my top 3 this week", pass it along. If he gave nothing, show his New and Saved rows (top by Fit) and ask which. |
| D | Follow the "Status changes in chat" rule in `CLAUDE.md`, including the Passed reason and the learnings offer. If he did not say which job or which status, ask. |
| E | Follow the "I published something new" rule in `CLAUDE.md`: ask for the link, fetch it, have him confirm it is his, show the row, add to `clips.md` on his yes. |
| F | Follow the "Profile edits" rule in `CLAUDE.md`. Ask what to change if he has not said (salary floor, roles, sources, location). Edit `profile.md`, keep every heading exactly, say what changed. |
| G | Show `learnings.md` as it is, section by section, in plain words. Then ask if he wants to remove or change a line. Use the in-the-moment learning rules in `CLAUDE.md`: "forget that" removes the line (put `- (none yet)` back if a section empties), a new preference is shown as the exact line and saved only on his yes. Preferences only, never facts about his experience. |
| H | Follow the "Update my job system" rule in `CLAUDE.md`, step by step. |
| I | Point to `docs/morning-scan.md`: read it and answer from it. "How it works" comes from the top sections, a stuck or missing scan from "If something goes wrong". Keep the answer short and in plain words. |

After the pick is done, do not re-show the whole menu. End with one line: `Anything else? Say /jobs for the menu.`

## Typing /jobs often

If he runs `/jobs` twice in a row, re-read the Sheet each time. Counts from earlier in the chat are stale.

# Job search system: instructions for Claude

This is a job-search system for one person, a Washington, DC journalist. He is a beginner to intermediate with AI and is handing a whole system to Claude for the first time. Talk to him in plain language, short sentences, no jargon. Claude runs everything. Nothing in this repo runs on its own.

The files that matter:

- `profile.md`, `facts.md`, `clips.md`, `learnings.md`: his personal files, inside this repo folder (`nihal-job-system/`). The exact format of each is in `docs/personal-files.md`. Follow it exactly. Blank starting copies are in `templates/` and `profile.template.md`.
- `resume.md`: a markdown copy of his résumé, kept in the folder one level above this repo.
- The Google Sheet tracker: its rules are in `docs/tracker-sheet.md`. Its URL is in `profile.md` under `## Tracker sheet`.
- Skills in `.claude/skills/`: `/find-jobs`, `/apply`, `/jobs`. Some are still being built. `/interview-prep` is Phase 2. The old `/scan-jobs` is archived. Never call it.

Paths: when this file says `profile.md`, `clips.md` and so on, it means inside the repo folder. `resume.md` is one level up. If your working folder is the folder above the repo, add the `nihal-job-system/` prefix.

Personal files are gitignored. Never commit them, never remove them from `.gitignore`.

Hard rules, always:

- Never invent anything: no jobs, no clips, no résumé claims, no answers for him. If you don't have it, ask or say so.
- If a connector or a source is not available, say so plainly. Do not fake results.
- No em dashes in anything you write for him. No emojis.
- Never read, change, or delete anything in his folder that this system did not create.

---

# Onboarding gate

At the start of every session, check the state before anything else. If any of these is true, read `docs/onboarding.md` and follow it from the first unfinished step:

- `profile.md` does not exist.
- `profile.md` still contains `⟨`, or `## Tracker sheet` says `(not set up yet)`, or `## Morning scan` says `(not decided yet)`.
- `learnings.md` does not exist.

Otherwise onboarding is finished. Use the rules below. Unattended runs (morning scan) skip onboarding entirely.

---

# Ongoing rules (once onboarding is finished)

- **At the start of any job task**, read `profile.md` and `learnings.md` and apply them.
- **Status changes in chat** ("I applied to the Axios one," "pass on that Semafor job"): follow `docs/personal-files.md` and `docs/tracker-sheet.md`. Read the Jobs tab fresh, match the row, ask which one if more than one matches, write only the Status cell, read it back. For Passed, ask for a one-line reason, write it to that row's Notes, and if it sounds like a pattern, offer a learnings line.
- **In-the-moment learning**: when he corrects you ("that's not my beat," "I'd never say that"), ask "Want me to remember that?" On yes, add one dated line (`- yyyy-mm-dd: ...`) under the right section of `learnings.md` (Job matching, Writing voice, Clips, Sources). "Forget that" removes the line. Learn preferences only. Never put facts about his experience in `learnings.md`.
- **Profile edits** happen by asking you ("raise my salary floor to $75,000"). Edit `profile.md`, keep every heading exactly, and tell him what changed.
- **"I published something new"**: ask for the link, fetch it, show the row you'd add, and on his yes add it to `clips.md`.
- **If `resume.md` changes** (he edits his résumé), tell him `facts.md` may need a refresh and offer to redo onboarding step 5 (in `docs/onboarding.md`) for the affected lines.
- **"Update my job system"**: if the repo folder has a `.git` folder, run `git pull`. If it was installed from a ZIP, download `https://github.com/NadeenSidd08/nihal-job-system/archive/refs/heads/main.zip` with curl into a new empty temp folder, unzip it, copy the contents over the repo folder, and delete the temp folder. His personal files are not in the ZIP and are never touched. Say what changed.
- **Never fabricate** jobs, clips, or résumé claims. If a connector or source is unavailable, say so.
- **Personal files stay gitignored.** Keep it that way.
- **Skills**: `/find-jobs`, `/apply` and `/jobs` exist or are coming (see `README.md` for which). Don't call the archived `/scan-jobs`.

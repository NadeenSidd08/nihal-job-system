# Job Scan Console

A personal job-search system you run with Claude. It has four parts:

- **`/scan-jobs`** — a Claude skill that searches Indeed against *your* criteria, scores every posting with a personalized rubric, and writes a curated markdown report.
- **`/apply`** and **`/interview-prep`** — skills that take a job from "found" to "ready": `/apply` builds a per-job application package (tailored résumé, company research, honesty check), and `/interview-prep` turns a scheduled interview into deep research plus an interactive flip-card prep deck, built from that same package.
- **`profile.md`** — your search profile (target titles, location, salary floor, dealbreakers, scoring rubric). Built for you during onboarding; never committed to git.
- **The console** — a local web dashboard showing your profile and every scan report in a sortable results table.

Scans run inside Claude sessions (or on a daily schedule you can ask Claude to set up). The console is the dashboard over the results — your resume, profile, and reports never leave your machine.

**New here? Start with the [User guide](USER-GUIDE.md)** — a plain-language walkthrough in three parts: install and setup, every feature, and a troubleshooting FAQ.

## Prerequisites

- **Claude desktop app** with a connected folder (these instructions assume it's called `Claude-Workshop`)
- **Indeed connector** enabled on your Claude account — this is what `/scan-jobs` searches with
- Your **resume** as a PDF in your Claude-Workshop folder (Google Doc? Use File → Download → PDF — or connect the Google Drive connector and Claude can fetch it)
- macOS with `python3` — serves the console locally, and runs the honesty lint that `/apply` depends on; a different static file server can still serve pages, but `python3` itself is now required, not optional

## Fastest way

1. Make a new, empty folder for this
2. Put your résumé PDF in it
3. Open Claude Code in that folder
4. Add the Indeed connector to your Claude account
5. Paste this one message:

```
Please set up the Job Scan Console for me. Clone
https://github.com/ai-build-club-dc/job-scan-console.git into this folder,
then follow the setup instructions in its CLAUDE.md and onboard me step by
step. I'm new to this, so explain what you're doing as you go and ask me
one thing at a time. My résumé PDF is in this folder.
```

Claude takes it from there — cloning the repo, finding your résumé, and walking you through everything below one step at a time.

## Quick start

Already have a `Claude-Workshop` folder and just want a shorter prompt? Paste this one instead:

> Download https://github.com/ai-build-club-dc/job-scan-console into my Claude-Workshop folder, then follow the setup instructions in job-scan-console/CLAUDE.md and onboard me.

Claude will then, with you in the loop:

1. Find your resume and create `resume.md` — a machine-readable copy used for everything afterward
2. Build your fact registry, `facts.md`, from `resume.md`, have you confirm it, then ask a few questions about wins that never made it onto your résumé
3. Interview you: target titles, location, salary floor, job type, dealbreakers
4. Write **your** `profile.md`, including a scoring rubric personalized to your background
5. Start the console at http://localhost:8642/console/
6. Offer your first `/scan-jobs` run — and an optional automatic daily scan

## Manual setup

1. **Code → Download ZIP** on this repo, unzip into your Claude-Workshop folder as `job-scan-console`
2. Open Claude in that folder and say: *"Follow the setup instructions in CLAUDE.md and onboard me."*

## Everyday use

| You want | Do |
|---|---|
| A fresh scan | Ask Claude: `/scan-jobs` |
| Build an application package for a job | Ask Claude: `/apply <company or role>` — or `/apply <url>` for a job you found yourself (one link at a time; it comes in `unscored` and won't appear in the console). Nothing is written until you approve the manifest it prints |
| Change criteria | Ask Claude: *"raise my salary floor to $95K"* (edits `profile.md`; console updates on refresh) |
| Prep for a scheduled interview | Ask Claude: `/interview-prep` — or just say *"I have a first round with Acme on Thursday"*. Reads the `/apply` package for that job and builds research plus a flip-card deck into the same folder |
| Automatic daily scans | Ask Claude: *"schedule a daily morning job scan"* |
| See results | http://localhost:8642/console/ → Refresh after a scan lands |
| Console offline? | Ask Claude: *"start the job scan console server"* |

## What's in the repo

### 🤖 The three Claude skills — `.claude/skills/`

- **`scan-jobs/`** — the `/scan-jobs` skill: searches Indeed, scores against your profile, writes reports
- **`apply/`** — the `/apply` skill: builds a per-job application package, manifest-first
- **`interview-prep/`** — the `/interview-prep` skill: round-aware interview research and a flip-card prep deck
  - `templates/deck.html` — the deck engine Claude copies and fills
  - `example/` — a complete worked run (fictional "Amberlyn Health") to see finished output

### 🖥️ The dashboard

- **`console/index.html`** — the entire console: one self-contained file, no dependencies, reads `profile.md` + `reports/`

### 📖 Documentation

- **`README.md`** — this page
- **`USER-GUIDE.md`** — the guide: install, every feature, troubleshooting FAQ
- **`CLAUDE.md`** — instructions your Claude follows to onboard you and run the system
- **`spec.md`** — design spec for scanning, scoring, and reports
- **`docs/apply-package-spec.md`** — design spec for the `/apply` package and honesty check

### 🔬 The honesty-check machinery — `scripts/`

- **`honesty_lint.py`** — passive checker `/apply` runs over tailored résumés — reports, never blocks
- **`test_honesty_lint.py`** + **`fixtures/`** — its test suite and the sample résumés/registries it runs against

### 📄 Templates & housekeeping

- **`profile.template.md`** — skeleton Claude fills in to create your `profile.md`
- **`.gitignore`** — the privacy guarantee: keeps personal files out of git
- **`LICENSE`** — MIT

### 🔒 Created during use — never in git

Once you onboard, these appear on your machine; the `.gitignore` means they can't be committed or pushed, by you or anyone:

- `profile.md` — your search profile
- `facts.md` — your fact registry
- `resume.md` — your résumé transcription (lives in the folder *above* the repo)
- `reports/` — your scan reports
- `applications/` — your application packages and prep decks

## Updating

Ask Claude: *"pull the latest job-scan-console"*. Your profile, registry, reports, and application packages are untouched by updates.

---

Built at **AI Build Club DC** · MIT license

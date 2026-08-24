# Job Scan Console — User Guide

*A guide in three parts: **Part 1** gets it installed and running, **Part 2** walks through every feature, and **Part 3** is a troubleshooting FAQ. You don't need any coding experience — if you can paste a message into Claude, you can do all of this.*

---

## What is Job Scan Console?

Job Scan Console is a personal job-search system you run through Claude. Instead of scrolling job boards yourself, you tell Claude what you're looking for once, and from then on:

- Claude **searches Indeed** for you, against your own criteria
- Claude **scores every posting** out of 10 using a rubric built from *your* résumé
- Results are saved as **reports** you can browse on a local dashboard
- When you find a job you like, Claude builds a full **application package** — tailored résumé, company research, and an honesty check that verifies every claim in that résumé traces back to something real
- When an interview gets scheduled, Claude builds your **interview prep** — deep company and interviewer research, plus an interactive flip-card practice deck

Everything personal — your résumé, your preferences, your reports — stays on your own computer. Nothing gets uploaded anywhere.

### Install in one message

Ready to go right now? Open the Claude Code desktop app in a folder that has your résumé PDF in it, and paste:

```
Download https://github.com/ai-build-club-dc/job-scan-console into this folder, then follow the setup instructions in job-scan-console/CLAUDE.md and onboard me.
```

Claude handles the rest and walks you through setup one question at a time. (Full prerequisites and what to expect are in [Part 1](#part-1--find-it-install-it-start-using-it) — one of them: add the **Indeed connector** in Claude's Settings → Connectors first, or scans can't run.)

## Table of contents

**[Features at a glance](#features-at-a-glance)** — the whole system in nine numbered items

**[Part 1 — Find it, install it, start using it](#part-1--find-it-install-it-start-using-it)**

- [What you need before you start](#what-you-need-before-you-start)
- [Where to find the repo](#where-to-find-the-repo)
- [Install and set up — one message does it all](#install-and-set-up--one-message-does-it-all)
- [What onboarding looks like](#what-onboarding-looks-like)
- [Your first scan](#your-first-scan)

**[Part 2 — Feature guide](#part-2--feature-guide)**

- [The console (your dashboard)](#the-console-your-dashboard)
- [`/scan-jobs` — searching and scoring](#scan-jobs--searching-and-scoring)
- [`/apply` — building an application package](#apply--building-an-application-package)
- [`/interview-prep` — prepping for a scheduled interview](#interview-prep--prepping-for-a-scheduled-interview)
- [Changing your profile](#changing-your-profile)
- [The fact registry (`facts.md`)](#the-fact-registry-factsmd)
- [Automatic daily scans (optional)](#automatic-daily-scans-optional)
- [Updating to the latest version](#updating-to-the-latest-version)
- [Privacy — what stays and what leaves](#privacy--what-stays-and-what-leaves)

**[Part 3 — Troubleshooting FAQ](#part-3--troubleshooting-faq)**

- [Setup and install](#setup-and-install)
- [The console](#the-console)
- [Scanning](#scanning)
- [Applications and the honesty check](#applications-and-the-honesty-check)
- [Interview prep](#interview-prep)
- [General](#general)

---

## Features at a glance

*The whole system on one page. Everything here is explained in full in Part 2.*

### 🔍 Finding jobs

1. **`/scan-jobs`** — searches Indeed against your profile and scores every posting 0–10 with your personal rubric. Only 6+/10 makes the report, max 10 results.
   1. Paste extra job links in the same message to score them too.
2. **The console** — your read-only dashboard at `localhost:8642/console/`: profile, job boards, and every report in sortable tables.
   1. Click **Refresh** after anything changes.

### 📝 Applying

3. **`/apply <company>`** — builds a complete application package: job post, company research, tailored résumé, gap analysis, notes tracker, honesty check.
   1. Nothing is written until you reply **`go`**.
   2. Also takes a pasted URL — that job comes in unscored.
4. **Honesty check** (`lint-report.md`) — traces every résumé claim to your fact registry and flags anything unsourced.
   1. Confident in a flagged claim? **Attest** it once and the warning clears.

### 🎤 Interviewing

5. **`/interview-prep`** — turns a scheduled interview into a company brief, an interviewer dossier, and a flip-card practice deck built from your tailored résumé.
   1. Later rounds never overwrite earlier decks.

### ⚙️ Maintaining

6. **Fact registry** (`facts.md`) — the confirmed-facts table everything is checked against. Skippable at setup; say *"finish my registry"* anytime.
7. **Profile changes** — just tell Claude: *"raise my salary floor to $95K."*
8. **Daily scans** — *"schedule a daily morning job scan."*
9. **Updates** — *"pull the latest job-scan-console."* Personal files are never touched.

---

# Part 1 — Find it, install it, start using it

## What you need before you start

1. **The Claude Code desktop app** installed on a Mac.
2. **A folder for this project.** Any folder works, named anything you like — make a new one anywhere (like your Desktop) if you don't have one. These instructions just call it "your project folder."
3. **Your résumé as a PDF** (a `.docx` also works), saved inside that folder.
   - Résumé lives in Google Docs? Open it, click **File → Download → PDF**, and save the PDF into your folder.
4. **The Indeed connector** added to your Claude account. In Claude, open **Settings → Connectors** and add **Indeed**. This is what actually searches for jobs — without it, scans can't run.

## Where to find the repo

The project lives on GitHub at:

**https://github.com/ai-build-club-dc/job-scan-console**

You don't need a GitHub account, and you don't need to download anything from that page yourself — Claude does the downloading for you in the next step. (The page is worth a bookmark, though: it's where updates come from.)

## Install and set up — one message does it all

1. Open the Claude Code desktop app.
2. Open a session in your project folder (the app will ask which folder to work in — pick that one).
3. Paste this message, exactly as written:

```
Download https://github.com/ai-build-club-dc/job-scan-console into this folder, then follow the setup instructions in job-scan-console/CLAUDE.md and onboard me.
```

That's the whole install. Claude downloads the project, reads its built-in instructions, and walks you through setup one question at a time.

**Along the way, Claude will ask permission to run commands** — for example, to download the project or start your dashboard. A small prompt appears asking you to allow it. Clicking **Allow** is normal and expected; it's just Claude checking in before touching your machine.

## What onboarding looks like

Setup is a conversation, not a form. Here's what happens, in order — the whole thing takes about fifteen minutes:

1. **Claude finds your résumé** in the folder. If it can't, it asks where the file is.
2. **Claude writes `resume.md`** — a plain-text copy of your résumé that it reads from now on, so it never has to re-open the PDF.
3. **Claude builds your fact registry (`facts.md`).** It pulls every number, job title, date, and credential out of your résumé into a table, quoting your résumé word-for-word, and shows it to you to confirm. Then it asks a few questions about things you've done that never made it onto the résumé. This registry is what your tailored résumés get fact-checked against later, so it's worth doing carefully.
   - **In a hurry?** This is the longest step, and you can skip it — just say *"I'll do this later."* Job scanning works fine without it. What you lose until it's done: tailored résumés come out marked **UNVERIFIED**, with no honesty check and no gap analysis. Come back any time by telling Claude: *"Finish my registry."*
4. **Claude interviews you** about what you actually want: 2–3 target job titles (plus any you *don't* want to see), location, minimum salary, full-time vs. contract, dealbreakers, and how recent a posting has to be (48 hours is the default).
5. **Claude writes your `profile.md`** — your answers plus a scoring rubric personalized to your background.
6. **Claude starts your dashboard** and gives you a link: **http://localhost:8642/console/** — open it in your browser and check that your profile appears.
7. **Claude offers your first scan**, and asks whether you'd like a daily scan scheduled automatically.

## Your first scan

In the same session, type:

```
/scan-jobs
```

Claude searches Indeed for every combination of your target titles and locations, scores what it finds, filters out anything that fails your dealbreakers, and saves a report. When it's done, it gives you a short summary in chat — the full results are in the report, which you read on your dashboard.

That's it — you're set up. Part 2 explains everything you can do from here.

---

# Part 2 — Feature guide

## The console (your dashboard)

Your dashboard lives at **http://localhost:8642/console/** in your web browser. It has three panels, listed in the left sidebar:

- **Profile** — a read-only view of your search profile: target titles, location, salary floor, job type, dealbreakers, and freshness rule. Near the bottom, a collapsible **Scoring rubric** section shows exactly how your fit scores are calculated.
- **Job boards** — shows Indeed as **Connected**. Other boards appear marked **Coming soon** — they're not wired up yet.
- **Reports** — a dropdown of every scan you've run. Pick one and you get a sortable table of matches; click any row to expand it and see why the job was recommended, how the score breaks down, and the posting's main points.

![The console's Profile panel and Job boards sidebar, populated with sample data](docs/images/dashboard.png)
*Sample data — "Jordan Lee" is a fictional example profile, not a real person or account.*

Three things to know about how the console works:

- **It's read-only.** There are no settings to change in the browser. To change anything, you ask Claude in a chat session, and the console shows the update the next time you click **Refresh** (top right).
- **The Run scan button doesn't run scans.** Scans only happen inside Claude sessions. Clicking the button just tells you that — it's not broken.
- **If you ever see a Welcome screen instead of your profile**, setup didn't finish (or your profile file moved). The Welcome screen includes its own copy-paste prompt to get you back on track.

![The Welcome panel with three numbered setup steps and a copy-paste prompt](docs/images/welcome.png)
*The Welcome screen — this is what the console shows before onboarding is finished. Use its built-in Copy button; by this point the repo is already on your machine, so its prompt skips the download step.*

## `/scan-jobs` — searching and scoring

Type `/scan-jobs` in any Claude session opened in the `job-scan-console` folder. Each run:

1. Searches Indeed for every combination of your target titles and locations
2. Merges the results and removes duplicates
3. Scores each job out of 10 against your personal rubric
4. Drops anything that fails your hard filters — salary below your floor, too old, a dealbreaker, or an excluded title
5. Saves a report to the `reports/` folder, named by date and time

Only jobs scoring **6/10 or higher** make the report, capped at **10 results**, each with a real apply link.

**You can also hand Claude specific jobs.** Found something on LinkedIn or a company site? Paste the link (or the posting text) in the same message as `/scan-jobs`, and Claude scores it with the same rubric, right alongside the Indeed results.

### How fit scores work

Every job's score out of 10 is built from three parts:

- **Title match (0–4)** — how closely the role matches one of your target titles
- **Substance (0–4)** — how well the actual work matches your background; this part was personalized to you during onboarding
- **Logistics (0–2)** — how cleanly location and salary line up, versus friction like a hybrid requirement or a salary band that straddles your floor

Expand any row in the console and the score section shows exactly how it adds up, one line per part — like *Title 4/4: exact match on "Product Manager."*

![The Reports panel showing a fit-score table with an expanded rationale row](docs/images/reports.png)
*A sample report in the console — "Shoply" and "Meridian Health" are fictional test-fixture companies, not real employers.*

**Calibration anchors:** after your first scan, Claude offers to record a few real scored examples into your rubric (like *"Shoply Product Designer: 4+3+2 = 9"*) so future scans stay consistent with how the first ones were judged. Say yes — it keeps scoring stable over time.

## `/apply` — building an application package

Found a job worth pursuing? Ask Claude:

```
/apply <company name>
```

Add the role too if that company shows up more than once in your reports (like `/apply Shoply Product Designer`).

**Nothing is created until you approve it.** Claude first prints a **manifest** — the company, role, fit score, which report the job came from, the files it's about to write, and roughly how many web pages it plans to read for company research. Reply **`go`** to build it. Doing several at once? Reply `go, skip <company>` to leave one out.

Once you say go, Claude builds a folder — `applications/<Company>-<Role>/` — with six files:

| File | What it's for |
|---|---|
| `job-post.md` | The job posting, saved in plain text with a link back to the source |
| `company-context.md` | Research on the company — what they do, size, recent news, why it might fit — every claim linked to a page Claude actually read |
| `tailored-resume.md` | Your résumé, rewritten for this specific job |
| `gap-analysis.md` | What the posting asks for, checked against your fact registry — what you clearly show, partly show, or don't |
| `notes.md` | A tracker for this application — status, dates, anything you want to jot down as things move |
| `lint-report.md` | The honesty check on the tailored résumé (explained below) |

### Applying to a job you found yourself

You can paste a link straight into `/apply`:

```
/apply https://boards.greenhouse.io/acme/jobs/12345
```

One link at a time — `/apply` can't take multiple links in one message, or mix a link with a company name. Two things are different about a link-sourced job:

- **No fit score.** Nothing has scored it against your rubric, so the package shows `unscored` instead of a number. The score is normally your early warning about whether a job is worth the effort — with a pasted link, that signal isn't there.
- **It won't appear on your dashboard.** The console only shows scan reports, and a pasted-link job never becomes one. The package still gets built in `applications/` — it's just not on the dashboard. That's expected, not a failure.

### The honesty check (`lint-report.md`)

This file lists every factual claim in your tailored résumé and says, for each one, which row of your fact registry backs it up — or **NO SOURCE** if nothing does. It also automatically flags numbers that don't match your registry, dates that don't line up, credentials it can't trace, and self-reported claims you haven't confirmed yet.

Two labels appear:

- **ERROR** (and NO SOURCE) — a claim with nothing behind it. Check these closely before sending anything.
- **WARN** — usually a self-reported claim: something you *told* Claude rather than something on your résumé. If you're confident in it, tell Claude to **attest** it — a one-time *"I stand behind this and can defend it in an interview"* — and it stops warning about that claim forever.

Edited the tailored résumé by hand afterward? Ask Claude to **re-lint that folder** and the report refreshes against your edits without rebuilding anything else.

### ⚠️ What "passing the check" does NOT mean

Read this before you send anything `/apply` produces to a real employer.

The check verifies one narrow thing: that each claim **traces to a source**. It cannot tell whether a claim is a *fair description of what actually happened*. If your registry says you "coordinated sprint planning" and the tailored résumé says you "owned the product roadmap," the check sees a traceable claim and stays silent — it can't notice that "coordinated" quietly became "owned."

So a clean report means every claim is sourced — not that the résumé is honest. Before you send anything, read the tailored résumé line by line yourself. If there's anything you couldn't say out loud and defend when a recruiter asks "tell me more about that," reword it down to what actually happened. **The check tells you what's sourced. Only you can tell what's true.**

## `/interview-prep` — prepping for a scheduled interview

When one of your applications turns into an actual interview, ask Claude — either type `/interview-prep`, or just say it naturally:

> *"I have a first round with Acme Health on Thursday, with Dana Whitcomb."*

Claude finds that job's application folder (the one `/apply` built), reads the tailored résumé and job posting already in it, and asks one combined question for whatever's missing: which round it is, who you're meeting (say "don't know yet" if you don't — but a name is what unlocks the interviewer research), and what kind of interview it is — recruiter screen, hiring manager, panel, technical deep-dive, and so on. The type shapes what the deck emphasizes.

Then it builds, into that same folder:

- **A company brief** — market position, competitors, funding, leadership, recent news, and where your role sits
- **An interviewer dossier** (when you named someone) — their role, career arc, what they'll likely probe, and what to ask them back. Anything Claude couldn't confirm is listed as unconfirmed instead of guessed.
- **The prep deck** (`interview-prep.html`) — flip cards: likely question on the front, your talking points on the back, plus collapsible panels for what you want to check five minutes before the call. It's a single file that opens in any browser — including on your phone, with no internet.

Three things worth knowing:

- **Every talking point comes from your own tailored résumé.** If the posting asks for something you haven't done, the deck names the gap and coaches you to bring it up yourself — a gap you raise first is a conversation; a gap the interviewer finds is a problem.
- **Round 2 doesn't overwrite round 1.** Each round's deck is a new file, and old decks are never touched. After a round, jot rough notes about how it went into the folder (Claude can tell you where) or just tell Claude what happened — the next round's deck is shaped by what actually happened in the room.
- **Claude will ask you for stories.** Résumés carry results, not the situations behind them — so when the deck needs detail your résumé doesn't hold, Claude asks you instead of making it up, and offers to save your answers so later rounds get better automatically.

No application package for the job yet? You can still prep — give `/interview-prep` the company name or the posting link, and it builds a clearly-marked "prep-only" folder instead. And keep the interviewer research to yourself — it's prep, not something to share around.

## Changing your profile

Your profile isn't a settings page — you just tell Claude what to change, in a session opened in the `job-scan-console` folder:

- *"Raise my salary floor to $95K"*
- *"Add Product Owner to my target titles"*
- *"Switch me to remote only"*

Claude edits the file, and your next scan (or a click of **Refresh** on the dashboard) reflects it immediately. Résumé updates work the same way — ask Claude to update `resume.md`.

One caution: **if your résumé changes, mention it to Claude** so it can refresh the fact registry rows that quoted the old wording — otherwise the honesty check is silently comparing against text that no longer exists.

## The fact registry (`facts.md`)

The registry is the table of confirmed facts behind the honesty check. Everyday phrases worth knowing:

- *"Finish my registry"* — if you skipped the registry during setup, this runs that step now. Claude will also offer to re-check any application packages that were built without it, which is when their UNVERIFIED banners come off.
- *"Attest that claim"* — upgrades a self-reported fact to one you formally stand behind, silencing its warning.
- Saying **"no"** to a fact marks it *declined* — Claude records that you disavowed it, which is different from just not confirming it yet.

## Automatic daily scans (optional)

Rather not remember to run `/scan-jobs`? Tell Claude:

> *"Schedule a daily morning job scan."*

From then on, a fresh report lands each morning the Claude app is open at scan time. (Laptop was closed? Just ask for a scan manually.) Scheduled scans follow the same honesty rule as manual ones: if Indeed isn't reachable when one runs, the report says so plainly instead of inventing results.

## Updating to the latest version

Ask Claude: *"Pull the latest job-scan-console."* Your profile, résumé, fact registry, reports, and application packages are all excluded from updates by design — you get the newest features with your personal data untouched.

## Privacy — what stays and what leaves

**Stays on your machine, always:** your résumé, `resume.md`, `profile.md`, `facts.md`, every report, and every application package. All of them are blocked from ever being uploaded to GitHub, even accidentally.

**Leaves your machine, by design, only:**

1. Job searches, through your own Claude account's Indeed connector.
2. If you use `/apply`, the company-research step reads about five public web pages per application (the company's site, recent news, the posting itself). For scan-sourced jobs, none of this happens until you reply `go`. For a pasted link, Claude reads that one page first — so the manifest can tell you what it's about to build — but everything else still waits for your `go`.
3. If you use `/interview-prep`, the research step reads public pages the same way — the company's site and press for the brief, and for a named interviewer only their public professional footprint: their employer's site, their own published writing, conference bios.

Your résumé and facts are never sent anywhere in either case.

---

# Part 3 — Troubleshooting FAQ

### Setup and install

**Claude says it can't find my résumé.**
Make sure the PDF (or `.docx`) is inside your project folder — not on your Desktop next to it, and not still in Google Docs. If it's a Google Doc, use **File → Download → PDF** and save into the folder. Then tell Claude where it is.

**Claude keeps asking permission to run commands. Is something wrong?**
No — that's normal and by design. Claude checks in before running anything on your machine. Click **Allow**.

**Can I install without letting Claude download anything?**
Yes. On the GitHub page, click the green **Code** button → **Download ZIP**, unzip it into your project folder so the folder inside is named `job-scan-console`, then open Claude in that folder and say: *"Follow the setup instructions in CLAUDE.md and onboard me."*

**I skipped the fact registry during setup. Did I break something?**
No. Scanning works fully without it. Tailored résumés will carry an **UNVERIFIED** banner until you finish it — say *"Finish my registry"* whenever you're ready, and Claude will also offer to remove the banners from packages built in the meantime.

### The console

**The console won't load / "connection refused" / "can't connect."**
The little local server that shows the dashboard isn't running (it stops when your computer restarts, for example). Ask Claude: *"Start the job scan console server."* Then reload the page.

**I opened `index.html` by double-clicking it and the page looks broken.**
The console has to be viewed through the server address, not opened as a file. Ask Claude to start the server, then go to **http://localhost:8642/console/**.

**The console shows a Welcome screen, but I already finished setup.**
Your `profile.md` file is missing or got moved. Ask Claude to check — it can usually spot and fix this in seconds.

**I ran a scan but the new report isn't on the dashboard.**
Click **Refresh** in the top-right corner. The console reads files from disk and doesn't know about new ones until you refresh.

**I clicked "Run scan" and nothing happened.**
That button can't run scans — scanning only works inside a Claude session. Type `/scan-jobs` in Claude instead.

**My dashboard shows a job type I told Claude I *don't* want.**
Tell Claude — this happens if an excluded type got written into the wrong section of your profile. Claude fixes it by moving the exclusion to your Dealbreakers.

**I built a package from a pasted link but it's not on my dashboard.**
Expected — the dashboard only shows scan reports, and pasted-link jobs never become reports. Your package is in the `applications/` folder; ask Claude to open or list it.

### Scanning

**The scan says the Indeed connector is unavailable.**
Add the Indeed connector in Claude's **Settings → Connectors**, then run `/scan-jobs` again. Claude will never fake results when the connector is missing — it tells you instead.

**My scan found nothing.**
Usually your filters, not a bug. The freshness window defaults to 48 hours, and every job must clear your salary floor, dealbreakers, and score at least 6/10. Ask Claude to loosen one thing at a time — *"widen my freshness window to a week"* is a common first fix.

**The same jobs keep appearing in every scan.**
Expected for now — each scan is independent and doesn't remember previous ones. Repeats across reports are normal.

**A report file ends in `-FAILED.md`.**
A scheduled scan ran while Indeed wasn't reachable. The file explains why, and no results were invented. Run `/scan-jobs` manually once the connector is back.

**My scheduled morning scan didn't run.**
Scheduled scans only run while the Claude app is open at scan time. If your laptop was closed or the app wasn't running, just ask for a scan manually.

### Applications and the honesty check

**`/apply` says it can't find `facts.md`.**
Normal the first time if you skipped the registry (or set up before this feature existed). Claude offers to build it on the spot before continuing.

**`/apply` asks which report or job I meant.**
Normal when a company name matches more than one posting. Name the specific role or the report date and it narrows down.

**I asked Claude to "process my queue" and it said it can't see the queue.**
Also normal — the console's queue list lives only in your browser, where Claude can't see it. Claude shows you a numbered list of jobs to pick from instead.

**`lint-report.md` has NO SOURCE or ERROR rows. Did it crash?**
No — that's the report doing its job. Each flagged row is a résumé claim with nothing in your fact registry behind it. Check the claim against your registry and your own memory, fix or remove what you can't back, then ask Claude to re-lint.

**Every warning is about things I told Claude in the interview.**
Those are self-reported claims. If you stand behind one, tell Claude to **attest** it and the warning goes away permanently.

**The job link is behind a login wall or dead.**
For a job **from a scan report**: `/apply` still builds the package from the report's summary and marks it "degraded" — thinner job details, everything else normal. For a **pasted link**: there's no summary to fall back on, so Claude asks you to paste the posting text into the chat yourself. Paste it and the build continues; decline and `/apply` stops cleanly with nothing written.

**I edited the tailored résumé myself. Is the lint report now wrong?**
It's stale until you refresh it — ask Claude to *"re-lint that folder."* Only the report is redone; your edits are kept.

### Interview prep

**`/interview-prep` says a file is missing or the application is incomplete.**
It needs the tailored résumé and job posting that `/apply` puts in the application folder. Run `/apply` for that job first — or tell Claude to build a prep-only folder if you're prepping before applying.

**The interviewer research came back thin, or mentions LinkedIn being blocked.**
Normal — LinkedIn blocks automated reading. Claude leans on the company's own site and the person's published writing instead, and anything it couldn't confirm is listed as unconfirmed rather than guessed. A thin dossier means little was publicly available, not that something broke.

**I asked to prep a round and Claude stopped, saying a deck already exists.**
By design — finished decks are never overwritten. Double-check the round number: a second round gets its own new file next to the first.

**The deck's cards don't flip when I click them.**
Tell Claude — it's supposed to verify the deck works in a browser before handing it over, and it can rebuild the file.

### General

**Is my résumé being uploaded somewhere?**
No. Your résumé, profile, facts, reports, and packages never leave your machine. The only outbound traffic is the Indeed search through your own Claude account, and (during `/apply`, with your approval) reading a handful of public web pages for company research.

**How do I get new features later?**
Ask Claude: *"Pull the latest job-scan-console."* Your personal files are never touched by updates.

**Something else is wrong and it's not listed here.**
Describe the problem to Claude in a session opened in the `job-scan-console` folder — it has the project's full instructions and can usually diagnose from there.

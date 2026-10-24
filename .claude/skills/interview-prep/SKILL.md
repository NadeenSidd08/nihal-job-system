---
name: interview-prep
description: BEING REBUILT, do not run. Build round-aware interview prep for a scheduled interview — a company/interviewer research brief plus an interactive HTML flip-card deck — without ever overwriting an earlier round's materials. Use when someone has an upcoming interview and names a specific round to prep for (e.g. "I have an interview Thursday", "help me prep for the second round with Acme", "build my interview deck for the onsite"), or types "/interview-prep". Not for general job-search chatter, resume work, or applications with no interview scheduled.
---

> Kept from the original repo and adapted in Phase 2. It expects the old /apply package, so don't run it yet.

# Interview Prep

Builds a prep folder for one interview round: a company brief, an interviewer dossier when a name
is given, and an interactive HTML deck. This is the interview-scheduled trigger — it fires only when
someone names an actual interview, never proactively from general job-search talk. Every talking
point on the deck must trace back to that application's own `tailored-resume.md`; never invent a
claim to cover a job-description keyword.

## Configuration

This is a job-scan-console project skill — sessions run at the repo root, and no setup is needed.

- **Applications root** — `applications/` at this repo's root (no trailing slash): the same folder
  `/apply` writes its packages into. One folder per job application lives directly under it, each
  usually holding at least `tailored-resume.md` (the resume tailored for that job) and
  `job-post.md` (the posting) — written by `/apply`, or by hand, or by the prep-only path in
  step 3. This skill writes generated prep into that same folder, alongside them.
- **Deck engine** — `.claude/skills/interview-prep/templates/deck.html`, the flip-card CSS/JS
  structure to copy and fill.
- **Worked example** — `.claude/skills/interview-prep/example/`, a complete filled-in prep folder
  for one company/round, kept for shape reference.

## Locate the application folder

1. **Job-post URL or pasted posting in the invocation?** If the argument starts `http://` or
   `https://`, or posting text was pasted alongside the request, don't name-match it. Fetch the
   URL first (one fetch — a paste needs none; on a failed fetch, carry on and note it, the paste
   fallback in step 3c covers it) and extract the company and role, then go straight to the
   folder question (step 3) with the extracted job named in it. This is the only fetch that may
   happen before a folder is settled.
2. Otherwise, fuzzy-match the named company or role against the subfolder names under
   `applications`. **Clear match** → that's the folder; continue to step 4. **Ambiguous
   between two folders** → ask which one before continuing.
3. **No match — or URL/paste entry from step 1** → ask the folder question, one question:
   a. *"Is this job in one of your application folders, or does it not have one yet?"* — naming
      the job when it's known (URL entry: the extracted company and role). List **every**
      subfolder under `applications` as a lettered list (A, B, C…), ending with the
      option *"No folder yet — build a prep-only package."* Flag exactly one letter as
      **recommended**: the folder that most resembles the job, or the no-folder option when
      nothing does. Never present a lettered list without a recommendation.
   b. **Picked a folder** → that's the folder; continue to step 4. Leave its files alone: the
      folder's own `job-post.md` and `tailored-resume.md` stay the sources even when a URL was
      fetched in step 1 — the fetched copy is set aside, never written over a package's files.
   c. **No folder yet** → build a **prep-only** package:
   - **Job post** — use the JD already fetched in step 1 rather than asking again. No URL was
     given, or the fetch failed (login wall, JS-rendered page)? Say why and ask for a paste of
     the posting text. Write `job-post.md` with a provenance line — `provenance: user-supplied
     URL (<url>)` or `provenance: user-pasted (<url>)`. If they decline to paste and nothing was
     fetched, abort cleanly: nothing created.
   - **Résumé** — list what exists and ask which to use: the master résumé if one is findable
     (recommend it), any `tailored-resume.md` from another application folder (with the caution
     that it was written for a different job's wording), or a file they paste or point to. Copy
     the choice into the new folder as `tailored-resume.md` so every later step works unchanged.
   - **Folder** — create `applications/<Company>-<Role>/` (same naming convention as
     the existing folders) holding `job-post.md`, the résumé copy, and a `notes.md` that begins
     `status: prep-only (no application package)` with a `resume_source:` line recording where
     the résumé copy actually came from — the copy must never pass as a résumé tailored and
     checked for this specific job.
   Then continue to step 4 as if the folder had been found.

## Verify the source files — never ask for the job description

4. Before doing anything else, confirm both `tailored-resume.md` and `job-post.md` exist in the
   matched folder. If either is missing, say which one and stop. If `notes.md` exists and begins
   with an `INCOMPLETE` marker, stop and say so — the application's build never finished, so
   `tailored-resume.md` was never checked and the deck would be built from unverified claims.
   `job-post.md` is already there — for a prep-only folder it was just written in step 3 — so
   never ask for the job description here.

## tailored-resume.md is the facts source, and its limits

5. `tailored-resume.md` in the matched folder is the source of truth: every claim on the deck traces
   back to it. It is the resume for *that specific application*, so a different application folder
   means a different source file — never carry facts from one application's resume into another's
   prep.
6. A tailored resume is thinner than full career notes: it carries results, not the situation, the
   constraint, or the judgment call — the material STAR stories are made of. Work with what's on the
   resume, and where a STAR story needs detail the resume doesn't hold, ask the user for it rather
   than inventing it. Offer to save her answers into an optional `career-notes.md` in the application
   folder so later rounds can reuse them. Don't make that file required or block on it.

## Round is required

7. The round shapes the deck and is mandatory. Always ask who the interviewer is — never infer their
   absence from silence; people usually know a name and won't think to volunteer it, and a name is
   what unlocks the dossier. Fold this into ONE combined question: round number, interviewer name(s)
   with an explicit "don't know yet" option, the interview type from the lettered list below, and
   anything else you're missing — never a series of separate prompts. Flag exactly one type letter
   as recommended, keyed to the round: round 1 → A, round 2 → B, later rounds → judgment from
   what's known. When the round itself isn't known yet either, key the recommendation to what the
   folder shows instead: no existing `interview-prep*.html` deck → assume round 1 (recommend
   A · Recruiter screen); otherwise recommend by the next round number implied by the decks
   already there. Skip whatever's already been stated — "prep me for my recruiter screen" has
   already answered the type. The medium (phone/video/onsite) stays optional — take it if
   offered, never chase it.

   The type list, each with the deck emphasis it buys:
   - **A · Recruiter screen** — logistics, salary story, the arc of "why this move," crisp company knowledge
   - **B · Hiring manager** — STAR depth, how you'd own the work, team fit, name your gaps before they do
   - **C · Peer / panel** — collaboration stories, retellings that stay consistent, a question for each seat
   - **D · Technical / skills deep-dive** — the evidence behind every skills claim, walkthroughs of real work
   - **E · Case study / take-home debrief** — defend your choices, the tradeoffs, what you'd do with more time
   - **F · Executive / final** — vision, judgment, questions back that show range
   - **G · Other / not sure** — the user describes it, or the deck stays generic to the round
8. Determine the round number N from what's stated or implied ("round 2", "final round" — ask if
   genuinely ambiguous). Glob `applications/<application folder>/interview-prep*.html` to
   see which rounds already have decks, confirming N really is next.

## Round-N naming — never overwrite an earlier deck

9. Round 1 → `interview-prep.html`. Round N≥2 → `interview-prep-round<N>.html`. A later round's deck
   is a new file, never a rewrite of an earlier one — shipped decks are frozen. If the target
   filename already exists, stop and ask before touching it.

## Round 2+: the prior debrief drives the deck

10. If `research/round-<N-1>-debrief.md` exists, read it before building anything. It's user-authored,
    first-hand from the room, and it outranks researched company material on conflict. Let it drive
    round-N emphasis — what landed, what didn't, what to hit harder — and correct or drop any stale
    researched claim it contradicts rather than presenting both as equally live. This skill reads
    debriefs; it never writes them — debrief authoring happens after the round and is the user's own.
    Prior-round feedback given in chat instead of in a file — "they pushed hard on X" — carries the
    same first-hand weight as a debrief file and drives round-N emphasis the same way; the skill
    still never writes a debrief file from it, the user's words are used directly.

## Research layer — the company always, the interviewer when named

11. `research/company-brief.md` is built on every run, both branches. It's the deep company research
    behind `company-context.md` — market position, competitors, funding and ownership, leadership,
    recent press, and the strategic read on where this role sits. On a later round, refresh it with a
    dated section rather than rewriting it.
12. Interviewer named → also build `research/interviewer-<name>.md` per person: role and tenure,
    career arc, what they'll likely probe, what to ask them back. This is the branch that does
    LinkedIn legwork — fold whatever LinkedIn yields straight into the dossier; never write a
    separate `linkedin-raw-<name>.md` file. LinkedIn commonly blocks automated fetches (HTTP 999),
    and a file whose entire content is "nothing was retrievable" is noise. When it's blocked, say so
    in one line of the dossier's uncertainty register (step 15) and move on. Prefer the company's own
    site, published writing, and conference bios — primary sources for employment and point of view
    anyway. Named interviewers also shape the deck: their probable questions become cards in step 17.
13. No interviewer name → the company brief is the entire research layer, and the deck takes its
    emphasis from the interview type chosen in step 7 instead of a dossier — only type G falls back
    to generic-to-the-round. Say so plainly in the wrap-up, and note that re-running later with a
    name adds the dossier without redoing anything else.

## company-context.md — additive, never rewritten

14. If `company-context.md` doesn't exist yet in the application folder, write it fresh — see
    `example/company-context.md` for shape and depth (what the company does, business model/stage,
    why this role exists now, talking points, honest watch-outs, sourced). If it already exists,
    append a dated round-N update section rather than rewriting the file.

## Uncertainty register — never assert what you couldn't confirm

15. Every interviewer dossier ends with an uncertainty register: what couldn't be confirmed, stated
    plainly. Never assert an unconfirmed title or fact — if something is genuinely unresolved, say so
    and move on rather than picking the more flattering guess.

## Deck construction — point at the engine and example, don't inline a template

16. Copy the flip-card CSS/JS engine and card structure from `templates/deck.html` (front = question,
    flip = talking points, plus collapsible reference panels). `example/` shows a filled-in round-1
    deck for shape and depth reference, and how a later round's deck differs when one already exists.
17. Every talking point draws only from `tailored-resume.md`, plus this application's own
    `job-post.md`, any `research/` dossier built in steps 11-12, and the optional `career-notes.md`
    from step 6 where it exists. Never fabricate a claim to cover a JD keyword with no honest match —
    name the gap instead. Weight the card mix by the interview type's emphasis line from step 7,
    and show the type in the deck header beside the round. A named interviewer sharpens the deck
    further — the dossier's probable questions and the type's defaults compose; they don't compete.
    When the folder also holds a `facts.md`-derived `gap-analysis.md` or the registry itself is
    available, those may INFORM framing (e.g. which gaps to name first), but deck claims still trace
    ONLY to this folder's `tailored-resume.md` (+ `career-notes.md`) — a fact that lives only in the
    registry (e.g. self-reported, never surfaced in the résumé) does NOT become a deck claim.
    Write to the filename fixed in step 9.
18. Verify it renders before handing it over. The deck is interactive, self-contained HTML — confirm
    the cards actually flip and the reference panels expand, by opening it in a browser or serving
    the folder locally. A deck that looks right in source and dies on click is worse than no deck.

## Self-check (a prompted check, not a mechanized one)

19. There is no script to run here — read the finished deck end to end and trace every number and
    factual claim back to `tailored-resume.md` (and `career-notes.md` where used). Report anything
    that doesn't trace, split into three groups:
    - **Career-fact findings** — the user's own titles, metrics, dates, credentials. These are the
      ones that matter; flag every one.
    - **Company-side figures** — the employer's funding, headcount, market size, salary bands, and
      the job posting's own stated experience requirement. These are quoted from public sources or
      `job-post.md` itself, not the user's claims, and don't need to trace to the resume — cite where
      each came from (`company-context.md` or `job-post.md`) and move on. Don't let this group's
      volume bury the career-fact findings.
    - **Derived-analysis findings** — an analysis derived from and quoted out of the folder's own
      files (e.g. a `gap-analysis.md` tally) is cited to that file: it's the package's own judgment,
      neither the candidate's claim nor a public figure.
    - A posting's stated experience requirement (e.g. "2-5 years") belongs in the company-side group
      even though it reads like a number about the candidate — it's the posting's number, not theirs.

## Wrap

20. Summarize: which application folder (and whether it was created prep-only this run), which
    round and interview type, the filename just written, whether a prior
    debrief changed round-N emphasis (round 2+), whether `company-context.md` was created or appended
    to, whether the research layer fired and on whom, whether any STAR detail was requested and saved
    to `career-notes.md`, and the self-check findings from step 19, split into its three groups.

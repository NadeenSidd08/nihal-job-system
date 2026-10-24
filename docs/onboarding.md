# Onboarding

Claude follows this file when `CLAUDE.md` says setup is not finished. Paths are explained in `CLAUDE.md`.

## How to run it

- One question at a time. Wait for the answer.
- Before each step, say in one or two sentences what you are about to do and why ("Here's what I'm doing: reading your résumé so I can keep a clean copy Claude can use").
- Never make up an answer for him. If he says "I don't know," offer two or three concrete options with a recommendation, or skip and note it.
- Offer choices as lettered options (A, B, C), with your recommendation first and one line of why.
- After each step say, in one line, that it's done and what's next.

## Where to resume

Check these in order and start at the first one that is not done. Tell him: "Welcome back. I see we already did X. Picking up at Y."

| Step | Done when |
|---|---|
| 1. Repo in place | You are working inside a folder named `nihal-job-system` that has `docs/personal-files.md` |
| 2. Materials found | Nothing is saved for this step. If `../resume.md` exists, skip to step 4 and ask for the website link only if step 4 needs it. Otherwise do it. |
| 3. resume.md | `../resume.md` exists and is not empty |
| 4. Clips | `clips.md` exists and its status line says `approved`. If it says `draft`, resume at the review. |
| 5. Facts | `facts.md` exists and contains no "<" placeholder (write it only after he has skimmed it) |
| 6. Profile | `profile.md` exists with no `⟨` left |
| 7. Google and Sheet | `## Tracker sheet` in `profile.md` holds a real URL |
| 8. Learnings file | `learnings.md` exists |
| 9. Morning scan | `## Morning scan` in `profile.md` says On or Off |
| 10. Wrap up | Right after step 9 is recorded. It is a message, so just give it. |
| 11. LinkedIn alerts (optional) | Nothing is saved. Onboarding counts as finished without it. Offer it again whenever he asks. |

## Step 1. Get the repo in place

Do this only if you are NOT already inside a folder named `nihal-job-system` that contains `docs/personal-files.md`. (If you are, skip to step 2.)

He pasted a setup message in a folder he made. That folder is his working folder and holds his résumé and LinkedIn PDF. This file only reaches you if you were pointed at it, so if the setup message asked you to clone and then read `nihal-job-system/CLAUDE.md`, you are in this step because of that.

Say: "First I'm downloading the job system into this folder."

1. Run `git --version`.
   - If it prints a version, run `git clone https://github.com/NadeenSidd08/nihal-job-system.git`.
   - If it fails, or a Mac window pops up asking to install "command line developer tools," tell him: "Your Mac wants to install a developer tool. You can click Install (takes a few minutes) or Cancel. Either is fine. I'll use a simple download instead, which is faster." Then use the ZIP route.
2. ZIP route: run
   `curl -L -o nihal-job-system.zip https://github.com/NadeenSidd08/nihal-job-system/archive/refs/heads/main.zip`
   then `unzip -q nihal-job-system.zip`, then `mv nihal-job-system-main nihal-job-system`, then `rm nihal-job-system.zip`.
   Tell him: "Heads up: because of this download method, when you say 'update my job system' later I'll download the newest copy again instead of using git. Your own files are not touched either way."
3. If a folder named `nihal-job-system` already exists, do not overwrite it. Ask whether to use it.
4. Done when `nihal-job-system/docs/personal-files.md` exists. From here, read `nihal-job-system/CLAUDE.md` if you haven't (it is this file) and continue with step 2, using the `nihal-job-system/` prefix for repo files.
5. Write a two-line helper file in his working folder, `CLAUDE.md`, containing exactly:
   `@nihal-job-system/CLAUDE.md`
   `Before any job task, read nihal-job-system/profile.md first, then run the command.`
   Do not overwrite a `CLAUDE.md` that is already there.
6. Tell him: "From now on, open Claude in the `nihal-job-system` folder (inside the folder you made). That's where the commands live. We can keep going here for now."

If clone and download both fail (no internet, GitHub blocked): say so, and ask him to open https://github.com/NadeenSidd08/nihal-job-system, click Code, then Download ZIP, double-click the ZIP, and drag the folder into his working folder, renamed `nihal-job-system`. Wait.

## Step 2. Find his materials

Say: "Now I'm looking for your résumé and LinkedIn profile in this folder."

Look in the repo folder and the folder directly above it. Never search higher without asking.

- Résumé: a PDF or .docx whose name suggests a résumé or CV. If more than one, ask which.
- LinkedIn PDF: usually named `Profile.pdf`, or has his name and mentions LinkedIn. Open the first page to confirm.
- Website: look in his setup message. If the link isn't there, ask for it, and don't assume the page put it there: "What's the link to your website with your published articles?"

If something is missing, say exactly how to get it:

- Résumé: "Drop your résumé (PDF or Word) into this folder: <full folder path>. If it's a Google Doc, open it, choose File, Download, PDF, and save it there."
- LinkedIn: "On LinkedIn, open your own profile, click More (the button near the top of your profile), then Save to PDF. Move the downloaded file into <full folder path>."
- Then wait for him to say it's there, and look again.

Done when you have named the résumé file, the LinkedIn file, and the website link, and he has confirmed them. If he has no website, ask for 10 to 15 links to his best clips instead (step 4 fallback). If he has no LinkedIn PDF, continue and note that facts will come from the résumé and clips only.

## Step 3. resume.md

Say: "I'm making a clean text copy of your résumé so I don't have to re-read the PDF every time. Your original stays as it is."

Tell him: "Claude may ask permission to read the folder above. Say yes."

Read the résumé and write `resume.md` in the folder one level above the repo (his working folder), NOT inside the repo. Make it a faithful markdown copy: every role, date, outlet, number and bullet as written. Do not fix, improve, or shorten anything. Mark anything you could not read as `[unreadable]`.

Done when `../resume.md` exists. Tell him: "Done. From now on I read this copy. If you want your résumé changed, ask me and I'll change this copy." The original stays the formatting source of truth.

Fallback: if the file can't be read (scanned image, locked file), ask him to paste the text into chat, or export a different PDF.

## Step 4. Clips library

Say: "I'm reading your website to build a list of your published articles. You'll approve it before I use it."

1. If you don't have his website link yet, ask for it. Fetch it with WebFetch. Find the page or pages that list his articles. Follow pagination and "more" links up to about 25 clips, most recent and most prominent first. If there are far more, ask whether he wants the most recent 25 or a different selection.
2. Build `clips.md` from `templates/clips.md`, in the format in `docs/personal-files.md`. For each clip: headline as published, outlet, date (yyyy-mm-dd), a short beat label, link, one plain sentence of summary. Open the article only if the listing doesn't show the date or outlet. Leave a cell blank if you can't tell. Do not guess a date, outlet, or summary. Leave Star blank. Keep `Status: draft, not yet reviewed`. Delete the template's HTML comment.
3. Show him a short table (Headline, Outlet, Date, Beat, Summary) and say how many rows there are. Add: "The file also holds a link for each. Say 'show me the links' if you want to check them." Then ask:
   "A. Looks right, approve it as is. B. Remove or fix some rows (tell me which). C. Star your favorites first (tell me which ones). You can do B and C and then approve."
4. Apply his changes. Starred rows get `*` in the Star column. When he approves, change the status line to `Status: approved <today's date>`.

Done when `clips.md` says `Status: approved`.

Fallback: if the website can't be read (blocked, needs a login, loads nothing), say so and ask: "Paste links to your 10 to 15 best articles, one per line." Fetch each link to fill the row. If a link can't be fetched, ask him for the headline, outlet and date, and record only what he gives you.

## Step 5. Fact list

Say: "I'm making a short list of the facts about your career that I'm allowed to use in applications. Everything on it comes from your own material. You skim it once."

1. Build the facts from `resume.md`, the LinkedIn PDF (if he has one), and `clips.md`, in the format in `docs/personal-files.md`. Each line ends with its source: `résumé`, `LinkedIn`, or `clips`. Use the section headings from `templates/facts.md`. Numbers on the résumé (for example "led a team of 6") go under `## Numbers` with source `résumé`. Copy facts as written. Do not rephrase them into something stronger.
2. Show it and ask once: "Anything wrong or missing?" No line-by-line review is needed. Fix what he points out.
3. Ask two or three quick questions for wins that are not on paper, one at a time, for example:
   - "Did any story you wrote get picked up, cited, or followed up by another outlet?"
   - "Any award, fellowship, or recognition that isn't on your résumé or LinkedIn?"
   - "Any numbers I should know, like stories broken, sources built, or audience reached?"
   For each answer, write the exact line you'd add and ask "Is that wording right?" Only after he says yes, add it tagged `(source: he said, <today's date>)`. If he says no or "not sure," do not add it.
4. Write `facts.md` (from `templates/facts.md`, comment deleted) once he has skimmed and answered. Keep every section heading. If a section has nothing yet, write `- (none yet)` under it.

Done when `facts.md` exists with no placeholder left. If there is no LinkedIn PDF, say so.

## Step 6. Profile interview

Say: "Now I'll ask you a few questions about what you want. It takes about five minutes."

Ask one question at a time. Use examples drawn from his résumé and clips (name his actual outlets and beats, not generic ones). Offer a draft answer he can accept when you can, drawn from what you read.

1. Beats: "Based on your clips I'd say your beats are <X, Y, Z>. Which would you call your real beats, strongest first?"
2. Target roles: "What job titles are you looking for? For example, <two examples fitting his level>. Two to five is plenty." Then: "Any titles you never want to see?"
3. Opportunity types, in priority order: Staff, Fellowship, Freelance, Nearby field (for example comms, policy writing, think tank editing). "Which do you want, and in what order? You can leave some out."
4. Location: "Washington, DC area, remote, or both?"
5. Salary floor: "Is there a minimum salary you won't go below? It's fine to say none."
6. Dealbreakers: "Anything that rules a job out? For example unpaid work, a long commute, a shift schedule."
7. Sources: explain in one line each, then ask "Keep them all on? (A. Yes, recommended. B. Turn some off.)"
   - Axios, Semafor, NPR, CQ Roll Call, Washington Post, WAMU: the newsroom's own job listings, checked directly.
   - Punchbowl News, The Dispatch, The 19th: their careers pages, small but relevant.
   - JournalismJobs (DC): a journalism job board filtered to DC.
   - Mediabistro and Media Job Board: national media boards, filtered by me for DC and remote.
   - LinkedIn job alerts (Gmail): emails from LinkedIn that I read. This is how I see Politico, The Atlantic, The Hill and others I can't read directly.

Then write `profile.md` from `profile.template.md`, following `docs/personal-files.md` exactly:

- Keep every `##` and `###` heading as written. Delete the HTML comment.
- Background: 2 to 4 sentences from `resume.md`, the LinkedIn PDF, and `clips.md`. Nothing he hasn't got on paper.
- Opportunity types: list only the types he wants, in his order. Never mention a type he left out, not even to rule it out.
- Sources: all `[x]` unless he turned some off (`[ ]`).
- Scoring rubric: personalize it. Fit = role match (0 to 4) + beat and substance (0 to 4) + logistics (0 to 2). Floor 6. Write the anchors against his actual target roles, beats and clips. Example shape: Role match, 4 = a title that matches one of his target roles; 3 = same family with a different qualifier; 2 = adjacent; 1 = distant; 0 = unrelated. Beat and substance, 4 = the job centers on his first beat and his clips show it; 3 = on one of his other beats; 2 = partial overlap; 1 = general journalism; 0 = none. Logistics, 2 = location, salary and type all fit; 1 = one friction; 0 = serious friction. Use his real beats and outlets in the wording. Leave `### Calibration anchors` empty.
- `## Tracker sheet`: write `(not set up yet)`. `## Morning scan`: write `(not decided yet)`. Steps 7 and 9 replace these.

Read the profile back to him in a few lines and ask "Anything to change?" Fix it.

Done when `profile.md` exists with no `⟨` left.

## Step 7. Connect Google and create the Sheet

Say: "Now I'm checking that Claude can reach your Google Drive and Google Sheets. Gmail too, if you can, for LinkedIn alerts."

1. Use ToolSearch to look for each connector's tools: search `gmail`, `google drive` (or `create_file`), and `sheets` (or `update_spreadsheet`). Tool name prefixes differ per install, so match on the end of the name.
2. If any is missing, tell him exactly: "In the Claude desktop app, open Settings, then Connectors. Find <Gmail / Google Drive / Google Sheets>, click Connect, and sign in with your Google account. Tell me when it's done." Wait. Check again. Don't go on without Drive and Sheets. Gmail is only needed for LinkedIn alerts; if he can't connect it yet, continue and tell him the LinkedIn source stays quiet until he does.
3. Look in Drive for a file named `Job tracker`. If one exists, ask whether to reuse it or make a new one. If reusing, check each tab, header, dropdown and the hidden tab against the list below (an older Seen tab with 8 columns needs extending to 12) and fix what is missing.
4. Create the Sheet from scratch, exactly as `docs/tracker-sheet.md` says in "How onboarding creates his Sheet" and "Connector calls that worked" (calls 1 to 5). Read those sections now. In short:
   - Create an empty Google Sheet named `Job tracker` with the Drive connector (`create_file`, mime type `application/vnd.google-apps.spreadsheet`). Keep its `id` and URL.
   - Rename the first tab to `Jobs` (frozen row 1). Add `Scan log` (4 columns) and `Seen` (12 columns, `columnCount: 12`), each with frozen row 1.
   - Headers, in this order:
     - Jobs (A to M): Status, Fit, Type, Job title, Org, Location, Why it fits, Source, Posted, First seen, Link, Folder, Notes
     - Scan log (A to D): Date, Sources checked, Sources that failed, New jobs added
     - Seen (A to L): Link, Source ID (IDs joined with "; "), Org, Job title, Location, Fit, First seen, On Jobs tab, Type, Source, Posted, Why it fits
   - Bold header row with a background colour. Column widths and wrap on Why it fits as in call 4. Date format `yyyy-mm-dd` on Posted, First seen, Scan log Date, and Seen First seen (G) and Posted (K).
   - Dropdowns on rows 2 to 1000: Status (column A) exactly `New, Saved, Ready to apply, Applied, Interviewing, Offer, Passed, Closed`. Type (column C) exactly `Staff, Fellowship, Freelance, Nearby field`. Fit (column B) whole number 1 to 10. On Seen: column H `yes, no`, and column I the same four Type values as Jobs column C.
   - Status colours on column A: New light blue, Saved light yellow, Ready to apply light orange, Applied light green, Interviewing light purple, Offer stronger green, Passed and Closed grey.
   - Hide the `Seen` tab.
5. Verify before telling him it's done: read back row 1 of all three tabs (Seen A1:L1), check `Seen` is hidden and that the dropdowns exist (`get_spreadsheet`, call 9). Do not add any job rows.
6. Give him the Sheet link. Say: "This is your tracker. You'll see jobs show up here. Bookmark it." Replace `(not set up yet)` under `## Tracker sheet` in `profile.md` with the full URL.

Done when `profile.md` holds the URL and the read-back matched the headers.

If a call fails: say which one, retry once, then tell him plainly and stop. Don't write the URL until the headers check out. On the next session step 7 starts again and finds the half-made `Job tracker` in Drive (item 3).

## Step 8. Learnings file

Say: "One more file, and then we're nearly done."

Copy `templates/learnings.md` to `learnings.md`. Delete its HTML comment. Explain in two sentences: "This is a short list of what I learn about your preferences, like roles to skip or phrases to avoid. I never add a line without asking you first, and you can tell me to forget any of it."

Done when `learnings.md` exists.

## Step 9. Morning scan

Say: "I can look for new jobs every weekday morning on my own, so the Sheet is fresh when you wake up."

Say: "It runs while the Claude app is open and your Mac is awake, and it uses your normal Claude plan, with no extra charge."

Ask: "A. Turn it on, weekdays around 7:30 (recommended). B. Leave it off for now. You can turn it on anytime by asking."

If he says B: record `Off` and move on.

If he says A (or names another time):
1. Create the scheduled task using the prompt in `docs/morning-scan.md`, with his real path in place of `<his folder>` and the time from his answer (default weekdays 7:30).
2. Walk him through the rest of `docs/morning-scan.md` one step at a time, waiting for him after each: first Edit (Working folder to his `nihal-job-system` folder, model Sonnet, permission mode default), then "Run now" and clicking "Always allow" on each prompt, then waiting for "succeeded".
3. If he wants to skip the test run now, tell him the first scheduled run will stop at the prompts until he does it once, and record On anyway.

Record his choice under `## Morning scan` in `profile.md`: `On, weekdays at 7:30` (or his time) or `Off`. Replace `(not decided yet)`.

Done when `## Morning scan` says On or Off.

## Step 10. Wrap up

Give a summary of at most 5 lines of what is set up (résumé copy, clips, fact list, profile, Sheet with its link, morning scan choice). Then:

- The three things to say next: "find me jobs," "/apply <a job or a link>," and "/jobs" for a simple menu. Say which of these exist yet (see `README.md`). Do not tell him a command works if it isn't built yet.
- How the Sheet works: jobs arrive as New. "Ready to apply" means I have built the application for you and it is waiting on you. "Applied" is only set by you, in the Sheet dropdown or by telling me ("I applied to the Axios one"). I never mark anything Applied myself.
- What "show me more" does: each search adds the 10 best new matches to the Sheet and keeps the rest in memory. Say "show me more" and I'll add the next 10 without searching again.
- "Next time, open Claude in the `nihal-job-system` folder, not the folder above it."
- Last, offer the optional LinkedIn alerts: "Want to set up the LinkedIn job alerts now, or tomorrow? Either is fine." If now, go to step 11.

## Step 11. LinkedIn job alerts (optional, now or tomorrow)

Say: "Some employers, like Politico, The Atlantic and The Hill, block me from reading their job pages. LinkedIn job alert emails are how I see those jobs. You set up two or three alerts once, and LinkedIn emails them to you every day."

Walk him through it, one alert at a time (suggest titles and location from `profile.md`):

1. On linkedin.com, click Jobs, search for one of your target roles, for example `<role from his profile>`, and set the location to <his location>.
2. Add filters if you like (Date posted: past week works well).
3. Turn on the "Set alert" switch, and choose Daily and Email.
4. Repeat for the second and third role.

Done when he says the alerts are on. If Gmail is connected, offer to look for LinkedIn's "job alert" confirmation emails to check. If he wants to do it later, that's fine. Leave the `LinkedIn job alerts (Gmail)` line in `## Sources` ticked `[x]` either way. `/find-jobs` will report "no alert emails yet" instead of a failure. He can ask you to walk him through it anytime.


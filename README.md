# Job search with Claude

This helps you find journalism jobs, track them in a Google Sheet, and tailor your résumé, cover letter, and clips for each one with Claude.

## How to start

You start from the onboarding page Nadeen sends you (link to be added). It tells you to make a new folder, put your résumé and LinkedIn PDF in it, open Claude in that folder, and paste a setup message. Claude downloads this system and then walks you through setup one question at a time. It takes about 30 minutes. If you stop halfway, open Claude in the `nihal-job-system` folder and it picks up where you left off.

## Where to open Claude

Open Claude in the `nihal-job-system` folder. If you open the folder above it instead, a small file that setup left there sends Claude to the right place.

## What you need

- The Claude desktop app on a Mac
- A paid Claude plan
- Gmail and Google Sheets
- The Indeed connector (optional, recommended)
- Your résumé
- Your LinkedIn profile as a PDF
- A link to your website

## The three commands

| Command | What it does | Status |
|---|---|---|
| `/jobs` | A simple menu that points you to the right next step | Available |
| `/find-jobs` | Finds journalism jobs that fit you (Indeed, JournalismJobs, Mediabistro, Daybook and your LinkedIn job alert emails) and adds them to your Google Sheet | Available |
| `/apply` | Tailors your résumé, writes a cover letter, and picks clips for one job | Available |
| `/interview-prep` | Prepares you for an interview. It gets adapted in Phase 2 | Coming later |

## Privacy

Your files live on your Mac and in your Google account. Claude reads them only when you run a command. Nothing personal is ever committed to this repo.

## Credit

Built from job-scan-console by AI Build Club DC, MIT license.

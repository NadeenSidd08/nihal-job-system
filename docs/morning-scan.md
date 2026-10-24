# Morning scan

The morning scan is a scheduled task that runs `/find-jobs` for you on weekday mornings, so the Sheet is fresh when you wake up. This page is for you and for Claude when it sets the task up.

## What it does and when

Every weekday around 7:30 (you can change this), Claude opens your `nihal-job-system` folder, checks all the sources you ticked in `profile.md`, scores new jobs against your criteria, and adds the best ones to the Jobs tab of your tracker Sheet. It also reads your LinkedIn job alert emails. It never applies to anything, never marks anything Applied, and never sends an email. A run takes about 10 minutes.

## What it costs

No separate charge. A scheduled run uses your Claude plan's usage, the same as a normal chat. If you hit your plan's usage limit, the run is rejected unless you have usage credits turned on. Setting the task's model to Sonnet (step 2 below) keeps each run cheap.

## The app must be open and the Mac awake

The task only runs while the Claude desktop app is open and your Mac is awake. If a run is missed, Claude does one catch-up run for the most recent missed time (within 7 days) when the Mac wakes or the app opens. To keep the Mac awake, go to Settings > This computer > System and turn on "Keep computer awake". Closing the laptop lid still puts it to sleep.

## Set it up once (about 5 minutes)

1. **Claude creates the task.** Ask Claude: "Set up my morning scan." Claude creates a scheduled task with the prompt below and your time (default weekdays 7:30). During onboarding, step 9 does this for you. Claude replaces `<his folder>` with your real path.

   ```
   Open the folder <his folder>/nihal-job-system. Read CLAUDE.md there and follow it. This is an unattended run: skip onboarding. Run the find-jobs skill in unattended mode. Never ask me a question. If something needs an answer, skip it and mention it in the summary. Do not edit, create, or delete any file in the folder. Only write to my tracker Sheet, as the skill describes. End with a summary of exactly 3 lines: how many new jobs were added to the Sheet, the best one (title and company), and anything that was skipped or did not work.
   ```

2. **Check the task's settings.** In the Claude desktop app, open **Scheduled** in the sidebar, click the task, then **Edit**. Set:
   - **Working folder** to your `nihal-job-system` folder. A task that Claude creates starts with the folder of the chat it was made in, which is wrong, so do not skip this.
   - **Model** to Sonnet.
   - **Permission mode** to the default (ask).
   Save.

3. **Do one supervised run.** Click **Run now**. Claude will stop and ask permission as it goes. Click **Always allow** on each prompt as it appears. Expect prompts for: reading files, each fixed curl line, each of the four WebFetch sites, Gmail search and read, and Google Sheets read and write. "Always allow" is saved on this task, so later runs do not ask again.

4. **Wait for "succeeded".** The first run takes about 10 minutes. When the task says succeeded, you are done.

## What you will see each morning

- A short summary in the task's chat, 3 lines: how many jobs were added, the best one, and anything skipped.
- New rows in the Jobs tab of your Sheet, with Status New. Open the Sheet and work from there.

## Pause, change the time, or stop it

In the desktop app, open **Scheduled** and click the task.
- **Pause:** switch the task off. Switch it on to resume.
- **Change the time:** click Edit and change the schedule.
- **Stop for good:** delete the task. Then tell Claude, and it will change `## Morning scan` in `profile.md` to Off.

You can also ask Claude to do any of these.

## If something goes wrong

- **It stalled.** A prompt is waiting for an answer. Open the task, find the run in its History list, open it, and answer the prompt with "Always allow". Later runs will not stop there. You can see and undo what you approved in the task's "Always allowed" panel.
- **Scans stopped and the app shows a yellow "sign in again" banner.** Sign in again, then click Run now once.
- **A run stalls on a prompt after day one.** Tell Nadeen which site or tool it named.
- **It did not run.** The app was closed or the Mac was asleep at that time. Open the app and wake the Mac. One catch-up run happens on its own. See "Keep computer awake" above.
- **The summary says "no LinkedIn alert emails".** There were no job alert emails from LinkedIn in the last 3 days. Set up job alerts on LinkedIn for journalism roles in Washington, DC, with email delivery, and they will be picked up on the next run.
- **A source did not work.** The summary says which one. The other sources still ran. Tell Claude and it will check.
- **The run was rejected.** You are at your plan's usage limit. Wait for it to reset, or turn on usage credits.

For the list of permissions the scan uses, see `docs/permissions.md`.

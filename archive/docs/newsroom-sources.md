# Newsroom sources (removed from the scan 2026-10-25)

These sources were taken out of `/find-jobs` on 2026-10-25 (NAD-43). The commands below were tested live on 2026-10-24 and 2026-10-25. They are kept here so a newsroom source can be added back later. To bring one back, add its line to the fixed commands in `.claude/skills/find-jobs/SKILL.md`, add its host or domain to `docs/permissions.md`, add a `[ ]` line to `## Sources` in `profile.template.md` and `docs/personal-files.md`, and add its Source value to `docs/tracker-sheet.md`. Background on how each was found is in `docs/source-spike-2026-10-24.md`.

The old unattended rules still apply to anything added back: fixed text, copied character for character, one command at a time, no `&`, `;` or `wait`.

## Fixed curl lines (all need `jq`, found at `/usr/bin/jq`)

```
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/axios/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/semafor/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://boards-api.greenhouse.io/v1/boards/nationalpublicradioinc/jobs | jq -c '.jobs[] | {id, title, loc: .location.name, posted: .first_published[:10], url: .absolute_url}'
curl -s -A 'Mozilla/5.0' -m 30 https://api.lever.co/v0/postings/fiscalnote | jq -c '.[] | {id, title: .text, loc: .categories.location, posted: (.createdAt/1000|strftime("%Y-%m-%d")), url: .hostedUrl}'
curl -s -A 'Mozilla/5.0' -m 30 https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":0,"searchText":""}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":20,"searchText":""}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":0,"searchText":"WAMU"}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs -X POST -H 'Content-Type: application/json' -d '{"appliedFacets":{},"limit":20,"offset":20,"searchText":"WAMU"}' | jq -c '.jobPostings[] | {title, loc: .locationsText, postedOn, path: .externalPath}'
curl -s -A 'Mozilla/5.0' -m 30 https://19thnews.org/careers/ | sed 's/<[^>]*>/ /g' | tr -s ' \n\t' ' '
```

| Source | Sheet "Source" value | Notes |
|---|---|---|
| Axios (Greenhouse) | Axios | Posted = `first_published`. Axios "Talent Pool" and Politico "Future Talent Pool" postings are pipeline postings, not real vacancies: if otherwise relevant, keep and put `Talent pool posting, not a live vacancy` in Notes. |
| Semafor (Greenhouse) | Semafor | Same shape as Axios. |
| NPR (Greenhouse) | NPR | Same shape as Axios. |
| CQ Roll Call (Lever, FiscalNote) | CQ Roll Call | FiscalNote is CQ Roll Call's parent. Keep only editorial roles. |
| Washington Post (Workday) | Washington Post | First line is page 1 (offset 0). Run page 2 (offset 20) only if the last job on page 1 is still inside the 14 days. Never a third page. Dates are relative ("Posted 5 Days Ago"). "30+" is outside the window. Source ID = the `JR-` number in the path. Link = `https://washpost.wd5.myworkdayjobs.com/washingtonpostcareers` plus the path. |
| WAMU (Workday, American University) | WAMU | Page 1 (offset 0). Run page 2 only if page 1 returned 20 jobs and the last is inside the 14 days. Source ID = the number after the last `_` in the path. Link = `https://american.wd1.myworkdayjobs.com/AU` plus the path. The keyword search can miss a WAMU role that never says "WAMU". |
| The 19th | The 19th | Careers page text. Deadline only, no posted date. Also lists contract openings. |

The Workday lists give only a short summary. Judge from title and location. In an attended run a detail page can be opened: Washington Post `https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers{externalPath}`, WAMU `https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU{externalPath}`. Read `jobPostingInfo.startDate` and `jobPostingInfo.externalUrl`. Greenhouse single job: `https://boards-api.greenhouse.io/v1/boards/{board}/jobs/{id}`.

## Fixed WebFetch pages

Prompt used for each: `List every job posting on this page. For each give the title, the location, the apply link or email, and the posted date if shown. Return nothing else.`

| Source | Sheet "Source" value | URL | Notes |
|---|---|---|---|
| Punchbowl News | Punchbowl | `https://punchbowl.news/careers/` | curl gets a 403, so WebFetch. No dates. |
| The Dispatch | The Dispatch | `https://thedispatch.com/careers/` | No dates. |
| Media Job Board (Poynter, national) | Media Job Board | `https://mediajobboard.com/search` | Latest ~24 nationally, search parameters ignored. Keep only DC metro or remote. Source ID = the code in the link. |

For the static pages (Punchbowl, The Dispatch, The 19th) the Link is the job's own apply link if it has one. If the only way to apply is email, use the careers page URL as Link and put `Apply by email: <address>` in Notes.

## Profile lines that went with them

```
- [x] Axios
- [x] Semafor
- [x] NPR
- [x] CQ Roll Call
- [x] Washington Post
- [x] WAMU
- [x] Punchbowl News
- [x] The Dispatch
- [x] The 19th
- [x] Media Job Board (national)
```

## Hosts

boards-api.greenhouse.io, api.lever.co, washpost.wd5.myworkdayjobs.com, american.wd1.myworkdayjobs.com, 19thnews.org (curl). punchbowl.news, thedispatch.com, mediajobboard.com (WebFetch).

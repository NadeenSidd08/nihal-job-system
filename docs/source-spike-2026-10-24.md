# Job source spike: what Claude can read automatically

Tested 2026-10-24. Read-only. No logins, forms, or sign-ups. Tools: curl with a browser user agent, plus WebFetch and WebSearch for sites that rejected curl. No page contained text addressed to an AI.

## 1. Summary

Of 18 newsrooms tried, 6 expose a clean JSON feed with no login: Axios, Semafor and NPR on Greenhouse, FiscalNote (CQ Roll Call's parent) on Lever, and The Washington Post and WAMU on Workday. Three more (Punchbowl News, The Dispatch, The 19th) are readable static pages with no posting dates. Politico works only through a third-party mirror (Built In). The Atlantic, The Hill and Bloomberg Industry Group are blocked or unreachable, and Washingtonian, Washington City Paper, National Journal, Government Executive and NOTUS are unconfirmed or expose no listings. Of the boards, JournalismJobs.com works well with a DC filter and exact posted dates (month and day on list pages, full ISO date in the detail page JSON-LD). The Media Job Board (Poynter's board, jobs.mediajobboard.com) and Mediabistro both load but ignore URL search parameters, so they only give the latest roughly 20 postings nationally. IRE, NABJ, NAHJ boards are JavaScript-driven or search-form based and were not read. The main pattern: most large newsrooms hide behind an ATS with a public JSON endpoint (Greenhouse, Lever, Workday), and those are far more reliable than the careers pages themselves, which often return 403 to scripted fetches.

## 2. Results table

Dates column: "exact" = ISO or specific date; "relative" = "Posted N Days Ago"; "no" = none shown.

| Source | Type | Platform | URL or endpoint tested | Result | Dates? | Apply links? | Open roles today | Journalism-relevant today | Notes |
|---|---|---|---|---|---|---|---|---|---|
| Axios | newsroom | Greenhouse | https://boards-api.greenhouse.io/v1/boards/axios/jobs?content=true | works | exact (first_published, updated_at) | yes (absolute_url) | 36 | 16 editorial (reporters, editors, producers); 5 anchored in DC or Arlington | Careers page itself returns 403 to curl, API is fine. Many reporter roles are "Remote". Location is free text. |
| Semafor | newsroom | Greenhouse | https://boards-api.greenhouse.io/v1/boards/semafor/jobs?content=true | works | exact | yes | 21 | 7 editorial; 6 roles list DC | Contract roles flagged in title "(Contract)". Includes "Video Producer Fellowship". Most editorial roles are NY or remote. |
| NPR | newsroom | Greenhouse | https://boards-api.greenhouse.io/v1/boards/nationalpublicradioinc/jobs?content=true | works | exact | yes | 11 | 1 clear (Supervising Editor, Morning Edition) plus 1 senior content strategy | Token is nationalpublicradioinc, found from the NPR careers page. Careers URL on npr.org used for discovery returned 404; the link was in the page body. |
| FiscalNote (CQ Roll Call parent) | newsroom | Lever | https://api.lever.co/v0/postings/fiscalnote?mode=json | works | exact (createdAt in ms) | yes (hostedUrl) | 7 | 2 editorial (Editor, White House Judicial and Legislative; Managing Editor, Politics and Leadership) | All 7 are Washington, DC. cqrollcall.com/careers returns 404, so FiscalNote's Lever board is the working route. |
| The Washington Post | newsroom | Workday | POST https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs | works | list: relative ("Posted 2 Days Ago", "30+ Days Ago"); detail: exact startDate | yes (externalUrl in detail) | 50 | about 21 by title (reporters, editors, producers, opinion, design, plus some business and comms) | Needs POST with JSON body. Detail GET: .../washingtonpostcareers + externalPath. Tenant found on corporate.washingtonpost.com/careers. Max 20 per page, use offset. |
| WAMU | newsroom | Workday (American University tenant) | POST https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs with searchText "WAMU" | works (filtered by keyword) | relative | yes | 5 | 3 (Producer 1A, 1A Plus Podcast Producer, Senior Reporter D.C. Politics) | WAMU's own wamu.org/careers page also lists them in the HTML. searchText "WAMU" may miss roles that do not use the word. Unconfirmed whether a cleaner filter exists. |
| Punchbowl News | newsroom | custom page, applications via LinkedIn or email | https://punchbowl.news/careers/ | works via WebFetch only (curl got 403) | no | partial (2 LinkedIn links, 2 email-only internships) | 4 | 2 (editorial internships) | Low volume. Business roles dominate. |
| The Dispatch | newsroom | custom (WordPress page) | https://thedispatch.com/careers/ | works | no | yes ("APPLY HERE" links, target not verified) | 3 | 1 (Social Video Producer) | Static page, easy to parse. No dates. |
| The 19th | newsroom | custom (WordPress page) | https://19thnews.org/careers/ | works | deadline only ("Apply by October 26, 2026") | "Click here for more information" | 1 (Chief Product Officer) | 0 | Says "no current contractor openings". Separate fellowship page /apply-for-a-19th-news-fellowship/ exists, not read. |
| Politico | newsroom | unknown (own site); mirrored on Built In | https://www.politico.com/careers (403 to curl, WebFetch refused); https://builtin.com/company/politico/jobs | partial (mirror only) | relative on mirror | yes (links go to Built In, then to Politico) | at least 3 visible on mirror, total unconfirmed | 1 visible (Social Media Editor) | Mirror shows only "recently posted". Real ATS unconfirmed. All Arlington, VA. |
| The Atlantic | newsroom | unknown | https://www.theatlantic.com/careers/ | blocked (curl 403, WebFetch refused) | n/a | n/a | unconfirmed | unconfirmed | Atlantic Media jobs board listing elsewhere said no jobs posted, undated. |
| The Hill | newsroom | unknown | https://thehill.com/careers/ | blocked (403 on curl and WebFetch) | n/a | n/a | unconfirmed | unconfirmed | |
| Bloomberg Industry Group / Bloomberg Government | newsroom | unknown | https://pro.bloombergindustry.com/careers/ | blocked (connection failed, no response) | n/a | n/a | unconfirmed | unconfirmed | Third-party boards show Arlington roles but they could not be verified as current. |
| Washingtonian | newsroom | unknown | https://washingtonian.com/jobs/ | partial | n/a | n/a | page has a "Jobs" heading but no listings in HTML | n/a | /careers/ is a 404. Its roles do appear on JournalismJobs (e.g. Editorial Internship, posted Oct 6). |
| Washington City Paper | newsroom | job board widget | https://jobs.washingtoncitypaper.com/ ("WCP Job Finder") | partial | unconfirmed | unconfirmed | unconfirmed | unconfirmed | Page loads (200) but I did not parse listings. It is likely a classified board, not the paper's own hiring. |
| National Journal | newsroom | custom | https://www.nationaljournal.com/about/careers | partial | n/a | none found | none shown | 0 | Page is generic text with no listings or ATS link. |
| Government Executive | newsroom | unknown | https://www.govexec.com/about/careers/ | blocked (404) | n/a | n/a | unconfirmed | unconfirmed | Atlantic Media brand; correct careers URL not found. |
| NOTUS | newsroom | unknown | https://www.notus.org/careers | unconfirmed | n/a | n/a | unconfirmed | unconfirmed | notus.org redirected to washingtonsun.com and the careers path returned 404. I could not confirm this redirect is intended, so treat as unreliable. |
| JournalismJobs.com | board | custom, server-rendered HTML | https://www.journalismjobs.com/job-listings?location=7 (7 = District of Columbia) | works | list: month and day, no year; detail: ISO datePosted and validThrough in JSON-LD | yes (relative link /{id}-{slug}) | 7 DC postings on page 1 (site-wide list has far more) | 5 of 7 DC (US News reporter, Bulwark x2, Washingtonian editorial internship, Courthouse News reporter) | The location filter works through `location=7` (found from the page's checkbox id; `state=` is silently ignored and returns the national list). robots.txt blocks several AI crawlers (CCBot, OAI-SearchBot, ChatGPT-User); no entry for Anthropic crawlers seen, but check this before production. Free keyword search at `?title=reporter`, freelance at `?jobType=4`. |
| The Media Job Board (Poynter's board) | board | mediajobboard.com (Poynter's site links to it) | https://jobs.mediajobboard.com/jobs | partial | relative ("Published 1 day ago") | yes | 7,496 site-wide; page shows 20 | not filterable | Keyword and location query parameters were ignored (same 7,496 count and same first page), so search is JavaScript-driven. Only the newest 20 are readable. Hits near DC on first page: 3 Washington DC, 1 Arlington. |
| Mediabistro | board | custom | https://www.mediabistro.com/jobs | partial | relative ("3 days ago") | yes | not counted | not filterable | `?q=` was accepted in the URL but results looked unfiltered. Many postings are "USA" with no city. Contains reposted Politico and Bloomberg roles, so expect duplicates. |
| IRE job board | board | GrowthZone, JavaScript | https://www.ire.org/jobs/ | blocked (page says "Loading job postings") | n/a | n/a | unconfirmed | unconfirmed | Postings load from a separate script URL that I did not identify. Post a job costs $50 per week, so volume is likely low. |
| NABJ Career Center | board | YM Careers style | https://nabjcareers.org/ | partial | unconfirmed | unconfirmed | unconfirmed | unconfirmed | Home page loads with a search form and category list (incl. Fellowships). The job search URL guess returned 404; actual search path not found. |
| NAHJ Career Center | board | YM Careers style | https://www.nahjcareers.org/ | partial | unconfirmed | unconfirmed | unconfirmed | unconfirmed | Same as NABJ. |
| AAJA jobs | board | n/a | https://www.aaja.org/jobs | blocked (404) | n/a | n/a | unconfirmed | unconfirmed | |
| Poynter fellowships list | fellowship list | n/a | https://www.poynter.org/fellowships/ | blocked (404) | n/a | n/a | unconfirmed | unconfirmed | No other fellowship list was verified. Fellowships found in practice came through Greenhouse (Semafor) and JournalismJobs (Washingtonian). |

## 3. Recommended source list for version 1

Newsrooms (API, with dates):
- Axios: GET https://boards-api.greenhouse.io/v1/boards/axios/jobs?content=true
- Semafor: GET https://boards-api.greenhouse.io/v1/boards/semafor/jobs?content=true
- NPR: GET https://boards-api.greenhouse.io/v1/boards/nationalpublicradioinc/jobs?content=true
- FiscalNote / CQ Roll Call: GET https://api.lever.co/v0/postings/fiscalnote?mode=json
- The Washington Post: POST https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers/jobs with body {"appliedFacets":{},"limit":20,"offset":0,"searchText":""}; detail GET https://washpost.wd5.myworkdayjobs.com/wday/cxs/washpost/washingtonpostcareers{externalPath} for exact startDate
- WAMU: POST https://american.wd1.myworkdayjobs.com/wday/cxs/american/AU/jobs with "searchText":"WAMU"

Newsrooms (static pages, no dates, low volume, use WebFetch):
- Punchbowl News: https://punchbowl.news/careers/ (WebFetch only)
- The Dispatch: https://thedispatch.com/careers/
- The 19th: https://19thnews.org/careers/

Boards:
- JournalismJobs.com, DC filter: https://www.journalismjobs.com/job-listings?location=7 (add `&jobType=4` for freelance; detail pages give ISO dates)
- Optional: Mediabistro and Media Job Board as "latest 20 nationally" sweeps, filtered by Claude for DC and remote

## 4. Do not promise

- Politico: own careers page blocks scripted fetches. Only a Built In mirror works and shows a partial list.
- The Atlantic, The Hill: 403 or refused fetch.
- Bloomberg Industry Group / Government: no reachable careers page from this environment.
- Government Executive, NOTUS: correct careers URL not found or redirects oddly.
- Washingtonian, Washington City Paper, National Journal: no readable listings on their own pages. Washingtonian roles do show up on JournalismJobs.
- IRE, NABJ, NAHJ, AAJA, Poynter fellowships: JavaScript-loaded, search path unknown, or 404. Fellowship coverage is therefore thin in v1.
- Media Job Board and Mediabistro keyword or location search: parameters ignored in plain fetches, so no reliable DC filter.
- WebFetch refuses some domains outright (Politico, The Atlantic). A cron-style skill using curl may behave differently than WebFetch: Punchbowl and Axios careers pages 403 to curl but Punchbowl worked in WebFetch, while the Greenhouse API works in both.
- LinkedIn job alert emails come from LinkedIn's job alert sender. That is a separate test and was not run here.

## 5. Patterns the /find-jobs skill must handle

- Location is free text. Examples: "Washington DC", "Washington D.C.", "Washington, District of Columbia, United States", "DC-Washington-TWP Headquarters", "Remote (Washington DC preferred)", "Arlington, VA". Normalize and include Arlington, Alexandria, and "Remote" as separate buckets.
- Dates: Greenhouse gives ISO `first_published` (use this as posted date, not `updated_at`, which changes on edits). Lever gives `createdAt` in epoch milliseconds. Workday list gives relative text ("Posted 30+ Days Ago" caps at 30) so call the detail endpoint for startDate on new roles. JournalismJobs list gives "Oct 23" with no year; use the detail JSON-LD `datePosted`. Boards give "3 days ago". Compute a daily "first seen" date and store it, since several sources have no dates.
- Pagination: Workday returns 20 per page (offset). The Media Job Board shows 20 per page of 7,496 with no working page parameter. Greenhouse and Lever return everything in one response.
- Duplicates: the same role appears on Mediabistro, Built In, the company ATS, and JournalismJobs (for example Washingtonian, US News). De-duplicate on normalized title plus employer plus city, and prefer the company ATS link as the apply link.
- Pipeline and "Talent Pool" postings (Axios "Talent Pool", Politico "Future Talent Pool") are not real vacancies. Flag them.
- Role type: encode contract and freelance from title text ("(Contract)", "Freelance", "Temp") and from JournalismJobs `jobType`. Fellowships and internships show up as titles, not as a field.
- Adjacent roles (communications, policy writing, think tank editing) will mostly appear in non-newsroom employers, which this spike did not cover. Axios and Semafor lists include many sales and marketing roles that need filtering out.
- Some pages need a browser-style user agent. Expect 403 from curl on several news sites even when WebFetch works, and the reverse.
- Check robots.txt for each board before putting it on a daily schedule.

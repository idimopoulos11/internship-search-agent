# Internship Search Agent

An AI agent I designed to run my Summer 2027 internship search. Three times a week it searches job boards, filters postings against my criteria, ranks what passes, logs it to a Google Sheet tracker, and emails me a short prioritized list.

Built with Claude (Anthropic) as a scheduled agent with tool access: web search and page fetching, Google Sheets, and Gmail. I wrote no application code. My work was the design: the instructions the agent runs on, the filtering and ranking logic, the tracker schema, and the changes I made after testing it.

## The problem

Hundreds of Summer 2027 postings go live between August and November, spread across company career sites, applicant tracking systems, and community-maintained GitHub lists. My search has specific constraints that keyword alerts handle badly:

- Role family: product management first, then AI operations and solutions, then business operations and venture, then marketing and growth.
- No roles that require real coding (I study chemical engineering, not computer science).
- Only five metro areas: New York City, the Bay Area, Seattle, Denver/Boulder, and Salt Lake City.
- Open to a May 2028 graduate.
- No traditional chemical engineering industries.

## How it works

Each run follows the same steps:

1. **Read the tracker first.** The agent reads my Google Sheet so it never re-sends a posting I have already seen, applied to, or rejected.
2. **Search.** Two GitHub internship trackers, apmlist.org, Greenhouse/Ashby/Lever/Workday job boards via targeted search queries, VC portfolio job boards, and a watch list of about 35 companies.
3. **Verify.** It opens each candidate posting to confirm it is open and to read eligibility, location, and pay. Postings it cannot read (some Workday and Microsoft pages block automated access) are kept but labeled "Unverified, open link to confirm."
4. **Filter.** Hard filters: Summer 2027 only, 2028-eligible, target cities only, no coding-heavy roles, no excluded industries. Within those, borderline fits are kept and labeled with the reason.
5. **Rank.** By role family, then location, then explicit 2028 eligibility, then nearest deadline.
6. **Log.** It appends one row per posting to the tracker: status, company, role, location, role family, priority, dates, eligibility, link, networking angle, and notes. It never edits or deletes my existing rows.
7. **Report.** It emails me the ranked list plus any tracker deadlines in the next 14 days.

## What I changed after testing

The first version was set to "err on the side of more." The first run returned **87 postings**, which was too many to act on. Reviewing them showed the filters were too loose: industrial-company product roles, cities I would not move to, and engineering roles that needed real coding. I made four changes:

- **Tightened the hard filters** to my five cities and to roles that do not require coding. I narrowed the 87 by hand to an 18-posting shortlist and added those to the tracker.
- **Added a "seen" list** so postings I had already reviewed and rejected are not resent.
- **Capped the email.** Above 15 postings, only the top 15 get full detail.
- **Made the agent report its own tool status.** On the first run the Google Sheets tool was not connected. The agent still sent the email, flagged the failure at the top, and saved the rows to a separate sheet so nothing was lost. I then connected Sheets and ran a test asking it to report whether the write worked.

## Reliability and failure modes

- **Stale listings.** Two shortlisted postings turned out to no longer exist when I went to apply. Aggregator listings lag behind company sites. The fix is to prefer the company's own posting and label anything sourced only from an aggregator.
- **Unreadable pages.** Some career sites block automated reading. Labeling these "Unverified" instead of dropping them trades a little noise for not missing real roles.
- **Duplicates.** The same role often appears under slightly different titles or location lists. The agent treats those as one posting.
- **Data formatting.** Sheets turns ISO dates into date values on write, so the agent writes them as text to keep the tracker consistent.

## Tradeoffs I considered

- **Recall versus noise.** The broad first version missed nothing but produced a list I could not use. The narrower version will miss some edge cases, which I accept in exchange for a list I act on.
- **Agent versus job alerts.** Job-board alerts are cheaper and faster but cannot apply multi-part criteria like "product role, not coding-heavy, 2028-eligible, in one of five cities." An agent can read and judge each posting.
- **Autonomy versus control.** The agent can add rows but never change or delete them. My own status edits always stay.

## Results

- First run: 87 postings found, about 15 verified by opening the posting directly; narrowed to an 18-posting shortlist.
- Runs every Sunday, Tuesday, and Thursday morning; each run takes about 10 to 15 minutes.
- The shortlist led directly to applications, including one role I found while checking a listing that no longer existed.

## What I would build next

- A verification pass that re-checks every "New" row weekly and marks closed postings.
- Automatic application-deadline reminders on my calendar.
- A short weekly summary of what I applied to and what is pending.

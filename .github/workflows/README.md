# Daily Job Search

A GitHub Actions workflow that opens an issue every weekday morning with fresh job search links, filtered to postings from the last 24 hours.

## How it works

`.github/workflows/job-search.yml` runs on a schedule and creates a GitHub Issue containing a checklist of search links — one row per target job title, with an Indeed and a LinkedIn link each. GitHub emails a notification when the issue is created, so the morning list arrives in the inbox without anything needing to be opened.

Each issue also has empty **Applied today** and **Notes** sections at the bottom for tracking what actually got submitted.

## Schedule

Set to 13:00 UTC, weekdays — which is 9:00 AM in Toronto during daylight saving time.

```yaml
- cron: '0 13 * * 1-5'
```

**GitHub Actions cron is always UTC.** It does not follow local time and does not adjust for daylight saving. When Toronto switches to EST in early November, change the hour to `14` to keep it landing at 9 AM. Switch it back to `13` in March.

Scheduled runs are also best-effort — GitHub queues them during busy periods, so 9:00 can become 9:20 or later. Fine for a morning nudge, not suitable for anything time-critical.

## Running it manually

The workflow includes `workflow_dispatch`, which adds a **Run workflow** button:

**Actions** tab → **Daily Job Search** in the sidebar → **Run workflow**

Useful for testing changes without waiting for the next scheduled run.

## Changing the job titles

Edit the `titles` array near the top of the script block in the workflow file:

```javascript
const titles = [
  "Data Engineer",
  "Junior Data Engineer",
  // ...
];
```

Titles are URL-encoded automatically, so spaces and punctuation are fine as-is.

## Changing the search filters

The links use each site's own recency parameter to limit results to the past 24 hours:

- Indeed — `fromage=1` (days)
- LinkedIn — `f_TPR=r86400` (seconds)

Location is hardcoded to Toronto in both URLs. If the results ever look wrong, run the search manually in a browser and copy the resulting URL pattern back into the workflow — these sites change their query parameters periodically.

## Known quirks

**Scheduled workflows auto-disable after 60 days of repository inactivity.** GitHub sends a warning email first, and any commit to the repo re-enables it. If the morning issues quietly stop appearing after a couple of months, this is why.

**YAML is whitespace-sensitive.** If a run fails, the Actions tab shows a red X — click into it for the error. Indentation is the usual culprit.

**Old issues accumulate.** Close them as you go, or they pile up. The issue list doubles as a rough record of which days were worked.

## Repository structure

```
.
├── .github/
│   └── workflows/
│       └── job-search.yml
└── README.md
```

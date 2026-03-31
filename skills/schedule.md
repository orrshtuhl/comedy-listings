# Skill: Weekly Schedule & Automation Cadence

## Purpose

Defines when each workflow runs, in what order, and how to trigger the full weekly pipeline across all active cities.

---

## Weekly Schedule

| Day | Task | Skill File | Scope |
|---|---|---|---|
| Monday | Scrape this week's comedy shows | `skills/scrape-shows.md` | All active cities |
| Monday | Update website with new listings | `skills/update-site.md` | All active cities + homepage |
| Wednesday | Send MailChimp email blast | `skills/mailchimp-blast.md` | All active cities |
| Daily (evening) | Update version history page | `skills/update-changelog.md` | Whole project |

---

## Determining Active Cities

Before running any scheduled workflow, read all `.json` files in the `cities/` folder. Only process cities where `"status": "active"`. Skip cities with `"status": "pending"` or `"status": "inactive"`.

---

## Running the Full Pipeline Manually

To kick off the Monday workflow for all cities, say:

> "Run the comedy listings pipeline for this week."

Claude should then, for each active city in `cities/`:
1. Read `cities/[slug].json` for city config
2. Read `skills/scrape-shows.md` and scrape shows → save to `data/[slug]/shows.json`
3. Read `skills/update-site.md` and generate → save to `site/[slug]/index.html`
4. Regenerate `site/index.html` homepage

Then report back: how many cities processed, how many shows found per city, any sources that failed.

To kick off the Wednesday email for all cities, say:

> "Send this week's MailChimp blasts."

Claude should then, for each active city:
1. Read `cities/[slug].json` for config (including `mailchimp_audience_id`)
2. Read `skills/mailchimp-blast.md` and compose/send the email

To run for a single city only, say:

> "Run the comedy listings pipeline for Philadelphia."

---

## Setting Up Scheduled Tasks (Cowork)

Use the `/schedule` command in Cowork to automate each step. Suggested prompts:

**Monday scrape + site update:**
```
Every Monday at 9 AM:
1. Read CLAUDE.md for project context
2. Read all city configs in cities/ where status is "active"
3. For each active city, read skills/scrape-shows.md and scrape comedy shows for the week
4. Save results to data/[city-slug]/shows.json
5. For each city updated, read skills/update-site.md and regenerate site/[city-slug]/index.html
6. Regenerate site/index.html homepage
7. Summarize: cities processed, show counts, any failures
```

**Wednesday email blast:**
```
Every Wednesday at 8 AM:
1. Read CLAUDE.md for project context
2. Read all city configs in cities/ where status is "active"
3. For each active city, read skills/mailchimp-blast.md and compose the weekly email
4. Send via MailChimp API (or save to drafts/[city-slug]/email-draft.html if API not configured)
5. Summarize: cities emailed, any failures
```

**Daily changelog update:**
```
Every day at 11 PM:
1. Read CLAUDE.md for project context
2. Read skills/update-changelog.md
3. Check git log for any commits made today
4. If commits exist, summarize them and update docs/changelog/index.html with one entry for today
5. If no commits, do nothing
```

**Note:** Scheduled tasks only run while your computer is awake and Claude Desktop is open.

---

## GitHub Sync (Manual for Now)

After each pipeline run, commit and push to GitHub:
```
git add .
git commit -m "Weekly update: [date]"
git push
```

This can later be automated as a final step in the scheduled task.

---

## Adding a New City to the Schedule

New cities are added via `skills/provision-city.md`. Once a city's config file is created and its `status` is set to `"active"`, it will automatically be included in the next scheduled run. No changes to the schedule skill are needed.

---

## Troubleshooting

- **Fewer than 5 shows for a city:** Check if that city's sources are still accessible. Update `cities/[slug].json` sources list if needed.
- **Wrong city getting wrong email:** Confirm `mailchimp_audience_id` in each city config is correct before sending.
- **Site not updating:** Confirm `data/[slug]/shows.json` was written successfully before running the site update step.
- **Scheduled task didn't run:** Verify computer was awake and Claude Desktop was open at the scheduled time.

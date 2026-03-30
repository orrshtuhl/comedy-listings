# Comedy Listings — Project Overview

## What This Is

An automated system that:
1. **Scrapes and compiles comedy show listings** for multiple cities on a weekly basis
2. **Publishes them to a static website** with a per-city page and a city directory homepage
3. **Sends a weekly MailChimp email blast** per city to that city's subscriber list
4. **Provisions new cities** on request, with manual approval before going live

This project runs on Cowork (Claude Desktop), with files version-controlled in GitHub. Claude runs all automation from the local folder — GitHub is for backup and version history, not runtime.

---

## Folder Structure

```
Comedy Listings/
├── CLAUDE.md                        ← You are here. Project overview and global instructions.
├── skills/
│   ├── scrape-shows.md              ← How to find and compile comedy show listings for a given city
│   ├── update-site.md               ← How to publish listings to the website (per-city + homepage)
│   ├── mailchimp-blast.md           ← How to compose and send the weekly email for a given city
│   ├── schedule.md                  ← When to run each workflow and in what order
│   └── provision-city.md            ← How to spin up a new city (with manual approval step)
├── cities/
│   ├── philadelphia.json            ← Config for Philadelphia (active)
│   └── [city-slug].json             ← One config file per city
├── data/
│   └── [city-slug]/
│       └── shows.json               ← Current week's scraped shows for that city (auto-generated)
├── site/
│   ├── index.html                   ← City directory homepage (auto-generated)
│   └── [city-slug]/
│       ├── index.html               ← City listings page (auto-generated)
│       └── archive/                 ← Past weeks' listings for that city
└── drafts/
    └── [city-slug]/
        └── email-draft.html         ← Current week's email draft for that city (auto-generated)
```

---

## City Config Files

Each city is defined by a JSON file in `cities/`. This is the source of truth for that city's settings. Example: `cities/philadelphia.json`.

Key fields:
- `slug` — URL-safe identifier (e.g. `philadelphia`)
- `name` — Display name (e.g. `Philadelphia`)
- `status` — `active` | `pending` | `inactive`
- `mailchimp_audience_id` — MailChimp list ID for this city
- `sources` — List of URLs/sources to scrape for this city
- `region` — State or region (for display and search context)

Only cities with `"status": "active"` are included in scheduled runs.

---

## Global Instructions for Claude

- **Always read the relevant skill file before starting a workflow.** Don't improvise — follow the skill file.
- **Always read the city's config file** (`cities/[slug].json`) before running any city-specific workflow.
- **Never put workflow logic inline in a conversation.** If you figure out a better way to do something, update the appropriate skill file in `skills/`.
- **Generated files go in `data/`, `site/`, or `drafts/`.** Don't clutter the root folder.
- **After completing any workflow, summarize what was done** so the user can review before committing to GitHub.
- **New cities must be approved before going live.** See `skills/provision-city.md`.

---

## Workflows

| Workflow | Skill File | Runs For | Cadence |
|---|---|---|---|
| Scrape shows | `skills/scrape-shows.md` | Each active city | Weekly (Monday) |
| Update website | `skills/update-site.md` | Each active city + homepage | After scraping |
| Send MailChimp blast | `skills/mailchimp-blast.md` | Each active city | Weekly (Wednesday) |
| Provision new city | `skills/provision-city.md` | New city request | On demand |

---

## Infrastructure (Status)

- [x] GitHub account — for version control
- [ ] GitHub repo — needs to be created and linked to this folder
- [ ] Site host — GitHub Pages or Netlify (TBD)
- [ ] MailChimp account — needs to be set up

---

## Notes

- Scheduled tasks only run while your computer is awake and Claude Desktop is open.
- Skill files are plain markdown — they're portable to other LLM tools if needed.
- Keep skill files updated as workflows evolve. The skill files are the source of truth.
- City config files are the source of truth for per-city settings. Update them, not the skill files, when city details change.

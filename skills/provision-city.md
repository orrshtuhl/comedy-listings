# Skill: Provision a New City

## Purpose

Spin up all the necessary files, folders, and services for a new city to be added to the Comedy Listings system. New cities always start in `"pending"` status and must be manually approved before going live.

---

## When to Run

Run this skill when:
- A user requests a city that doesn't exist yet
- You are manually adding a new city

Trigger phrase: "Add [City Name] to Comedy Listings" or "A user requested [City Name]"

---

## Step 1 — Check If City Already Exists

Before doing anything, check the `cities/` folder for an existing config file matching the requested city (by slug or name). If it already exists:
- If `status` is `active`: inform the user the city is already live and link to its page.
- If `status` is `pending`: inform the user it's already queued for approval.
- If `status` is `inactive`: ask whether to reactivate it.

---

## Step 2 — Generate the City Slug

Create a URL-safe slug from the city name:
- Lowercase
- Replace spaces with hyphens
- Remove special characters
- Examples: `New York City` → `new-york-city`, `Philadelphia` → `philadelphia`

---

## Step 3 — Research Sources for This City

Before creating the config, do a quick search to identify the best comedy-specific sources for this city. Look for:
- Local comedy clubs with online calendars
- City-specific Eventbrite/Ticketmaster comedy pages
- Local alt-weekly event listings (e.g. Time Out, Village Voice, Chicago Reader)

Aim for at least 3–5 reliable sources. These go into the config's `sources` array.

---

## Step 4 — Create the City Config File

Create `cities/[city-slug].json` with the following structure:

```json
{
  "slug": "city-slug",
  "name": "City Name",
  "region": "State Abbreviation",
  "status": "pending",
  "created_at": "YYYY-MM-DD",
  "mailchimp_audience_id": null,
  "mailchimp_signup_url": null,
  "site_url": null,
  "accent_color": "#e63946",
  "sources": [
    {
      "name": "Source Name",
      "url": "https://...",
      "type": "venue-direct | ticketing | city-guide | social",
      "notes": "Optional notes about this source"
    }
  ]
}
```

Set `status` to `"pending"` — never `"active"` — until manually approved.

---

## Step 5 — Create Folder Structure

Create the following empty folders (with `.gitkeep` files to preserve them in Git):
- `data/[city-slug]/`
- `site/[city-slug]/`
- `site/[city-slug]/archive/`
- `drafts/[city-slug]/`

---

## Step 6 — MailChimp Audience (Manual Step — Flag for User)

Each city needs its own MailChimp audience (mailing list). This cannot be done automatically until the MailChimp API is configured. Flag this as a required manual step:

> "⚠️ Manual step required: Create a new MailChimp audience for [City Name] and add the audience ID to `cities/[city-slug].json` under `mailchimp_audience_id`."

---

## Step 7 — Run a Test Scrape (Optional but Recommended)

Before requesting approval, optionally run `skills/scrape-shows.md` for the new city to verify the sources work and shows can be found. Save results to `data/[city-slug]/shows.json` as a preview.

---

## Step 8 — Request Manual Approval

After completing the above steps, present a summary for approval:

```
New city ready for review: [City Name]

Config: cities/[city-slug].json
Sources found: [N]
Test scrape: [ran / skipped] — [N shows found if ran]
MailChimp audience: [set / ⚠️ needs to be created manually]

To activate this city, say: "Activate [City Name]"
To discard it, say: "Remove [City Name]"
```

Do NOT set `status` to `"active"` until the user explicitly approves.

---

## Step 9 — Activation (On Approval)

When the user says "Activate [City Name]":
1. Open `cities/[city-slug].json`
2. Change `"status": "pending"` to `"status": "active"`
3. Confirm: "Philadelphia is now active and will be included in the next scheduled pipeline run."

---

## Notes

- The `pending` status is a safety gate — it prevents untested or incomplete city configs from being included in scheduled runs.
- City configs are the single source of truth. Never hardcode city-specific details in skill files.
- If a city is deactivated, set `status` to `"inactive"` rather than deleting the config file. This preserves the sources list and history.

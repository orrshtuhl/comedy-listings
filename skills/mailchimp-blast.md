# Skill: Send Weekly MailChimp Email Blast

## Purpose

Compose and send (or schedule) a weekly email to a city's MailChimp subscriber list with that city's comedy show listings.

---

## When to Run

Run mid-week (Wednesday recommended) for each active city, after `skills/scrape-shows.md` and `skills/update-site.md` have completed.

---

## Input

Before starting, read the city's config file: `cities/[city-slug].json`

Use the config to get:
- `name` — city display name
- `mailchimp_audience_id` — the MailChimp list ID for this city's subscribers
- `mailchimp_signup_url` — subscribe link to include in the footer
- `site_url` — link to the city's listings page (e.g. `https://yoursite.com/philadelphia`)

Also read `data/[city-slug]/shows.json` for the current week's listings.

---

## Prerequisites

- `data/[city-slug]/shows.json` must be up to date for the current week
- MailChimp account must be set up (see Infrastructure section below)
- MailChimp API key must be available

**Infrastructure status:** MailChimp account not yet created — update this file with API key location once set up. Each city needs its own audience (mailing list) in MailChimp; the audience ID lives in the city's config file.

---

## Email Format

### Subject Line
`🎭 [City Name] Comedy This Week — [Month DD]`

### Preview Text
`[N] shows this week in [City Name]. Here's what's on.`

### Body Structure

```
Hey [First Name | there],

Here's what's happening in [City Name] comedy this week:

---

[DAY, MONTH DD]

• SHOW/PERFORMER NAME
  Venue Name — Time — $Price
  [Short description if available]
  → Get Tickets

[repeat for each show, grouped by day]

---

View the full listings online: [site_url from city config]

Want to stop getting these? Unsubscribe here.
```

### Tone
- Friendly and concise — not promotional
- Let the listings speak for themselves
- One brief intro sentence max before the listings start

---

## Sending via MailChimp

### Option A: Via API (preferred when configured)
Use the MailChimp API (or Zapier MCP connector) to:
1. Create a new campaign targeting the city's `mailchimp_audience_id`
2. Set subject line, preview text, and from name
3. Upload the HTML email body
4. Schedule for Wednesday morning (9 AM ET) or send immediately

### Option B: Draft to file (use until MailChimp is set up)
Save the email as `drafts/[city-slug]/email-draft.html` for manual upload to MailChimp.
Create the folder if it doesn't exist.

---

## Output

- **If API connected:** Campaign created and scheduled/sent in MailChimp for this city's audience
- **If not connected:** `drafts/[city-slug]/email-draft.html` ready for manual upload

---

## Quality Checks Before Sending

- Subject line includes the correct city name and week date
- The correct `mailchimp_audience_id` is being used (never cross-send between cities)
- At least 5 shows are listed
- All ticket links are present and correct
- The "view online" link points to the correct city's site page
- Unsubscribe link is present (MailChimp adds this automatically if using their template system)

---

## Notes

- Each city has its own MailChimp audience — never send one city's listings to another city's list.
- MailChimp's free tier allows up to 500 contacts and 1,000 sends/month — fine for starting out with a few cities.
- A direct MailChimp MCP connector may be available; check the MCP registry. Otherwise use Zapier MCP as a bridge.
- Once the website is live, the "view online" link in every email should point to that city's page.
- Keep a consistent send day/time per city to build subscriber expectations.

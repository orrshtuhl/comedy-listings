# Skill: Update the Comedy Listings Website

## Purpose

Take scraped show data and generate two things:
1. An updated `docs/[city-slug]/index.html` for the city that was just scraped
2. An updated `docs/index.html` homepage listing all active cities

---

## When to Run

Run immediately after `skills/scrape-shows.md` completes for one or more cities.

---

## Prerequisites

- `data/[city-slug]/shows.json` must exist and be current for any city being updated
- `cities/[city-slug].json` must exist for each city
- The `docs/[city-slug]/` and `docs/[city-slug]/archive/` folders must exist (create them if not)

---

## Step 1 — Archive the Previous City Page

Before overwriting `docs/[city-slug]/index.html`, check if it exists. If it does:
- Copy it to `docs/[city-slug]/archive/YYYY-MM-DD.html` using the previous week's date (or today minus 7 days).

---

## Step 2 — Generate the City Listings Page

Read `data/[city-slug]/shows.json` and generate `docs/[city-slug]/index.html`.

**Page requirements:**
- Title: `[City Name] Comedy This Week — [Week of Month DD, YYYY]`
- Shows grouped by month, then by day within each month
- Each show is a single line in this format, linked to the ticket/info URL:
  `Weekday M/D Performer Name @ Venue Name $Price`
  Example: `Tue 3/31 Don McMillan @ Helium Comedy Club $76+`
- No start time displayed
- Shows tagged `open-mic` should be visually distinct (e.g. a subtle label or muted style)
- If a comedian has a qualifying YouTube clip, append a `(YT)` link after the listing line. Rules for qualifying:
  - Must be a stand-up comedy clip (not an interview, talk show appearance, or sketch)
  - Must be under 20 minutes long
  - Must have been published within the last 12 months
  - Search YouTube for `[comedian name] stand up comedy 2025` and extract results from `ytInitialData`
  - Format: `(<a href="https://www.youtube.com/watch?v=[id]" target="_blank">YT</a>)` appended after the main listing link — only "YT" is the hyperlink; parentheses are plain text
  - Skip if no qualifying video is found — don't force it
- If a comedian has flair data in `data/[city-slug]/flair.json`, render a second line below the listing inside a `<div class="show-flair">`. Only render this line if flair exists.
  - 👀 for press coverage: `👀 [Outlet Name]` — one per qualifying outlet (e.g. `👀 New York Times`)
  - 🍿 for streaming special: `🍿 [Platform Name]` (e.g. `🍿 Netflix`, `🍿 YouTube`). For YouTube specials, link the platform name to the video: `🍿 <a href="https://www.youtube.com/watch?v=[youtube_id]" target="_blank">YouTube</a>`
  - If multiple flair items, separate with two non-breaking spaces: `👀 New York Times&nbsp;&nbsp;🍿 Netflix`
  - Same font size and family as the rest of the page (16px Courier New); use muted color (e.g. `#555`) to read as secondary
  - Match flair to shows by the `performer` field in `shows.json` — look up by exact name
- Mobile-friendly layout (simple responsive CSS — no frameworks)
- Footer with: "Updated [date]", a "← All cities" link back to the homepage, a subscribe link (use `mailchimp_signup_url` from city config, or `#` placeholder if not yet set), and a "version history" link pointing to `../changelog/`

**Design principles:**
- Simple, fast-loading — no JavaScript required
- Black and white with one accent color (use `accent_color` from city config if set, otherwise default: `#e63946`)
- Readable on mobile without zooming
- Consistent layout across all city pages

---

## Step 3 — Regenerate the Homepage

After updating any city page, regenerate `docs/index.html` as a city directory.

**Homepage requirements:**
- Title: `Comedy Listings — Find Shows In Your City`
- List all cities where `status` is `active` (read from `cities/` folder)
- Each city entry shows: city name, state/region, and a link to its listings page
- Brief tagline: "Weekly comedy show listings, automatically updated."
- Link to request a new city (placeholder for now — will eventually point to a request form)

---

## Step 4 — Validate

Before saving, confirm:
- HTML is valid and well-formed
- All show URLs are present as clickable links
- Page title date matches the `week_of` field in `shows.json`
- Homepage links correctly to each active city page

---

## Output

- `docs/[city-slug]/index.html` — current week's listings for the city
- `docs/[city-slug]/archive/YYYY-MM-DD.html` — archived previous page
- `docs/index.html` — updated city directory homepage

---

## Deployment Note

Once the site host is configured (GitHub Pages or Netlify), pushing to the GitHub repo's `main` branch will auto-deploy the site. Until then, these files are local previews.

**Site host status:** Not yet configured — update this file when a host is chosen.

---

## Notes

- If `shows.json` has fewer than 5 shows, add a note on the city page: "Listings may be incomplete — check back soon."
- Don't delete archive files — they're a historical record.
- Keep HTML self-contained (inline CSS is fine for now; no external dependencies).
- All city pages should share the same visual design so they feel like one site.

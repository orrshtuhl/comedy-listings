# Skill: Scrape Comedy Show Listings for a City

## Purpose

Find and compile upcoming comedy shows for a given city for the next 6 months. Output a structured list saved to `data/[city-slug]/shows.json`.

---

## When to Run

Run weekly (Monday recommended) for each active city. Each run refreshes the full 6-month forward calendar — adding newly announced shows and removing any that have passed or been cancelled.

---

## Input

Before starting, read the city's config file: `cities/[city-slug].json`

Use the config to get:
- `name` — the city's display name (for search queries)
- `region` — state/region (to narrow search results)
- `sources` — the list of URLs and sources to check for this city

---

## How to Search

For each source in the city config's `sources` list, fetch or search it for comedy events in the coming week. Use web search tools when available; otherwise fetch URLs directly.

### Generic Sources (apply to any city)
If a city config doesn't yet have a curated source list, fall back to these search patterns:
- Eventbrite: `https://www.eventbrite.com/d/[state]--[city]/comedy/`
- Ticketmaster: search `comedy [City Name]` on ticketmaster.com
- Facebook Events: search `comedy [City Name] this week`
- Local alt-weekly or city guide (e.g. Time Out, Do312, Philadelphia Magazine)

### Source Quality Tiers
Prefer sources in this order:
1. **Venue-direct** — the comedy club's own calendar (most reliable)
2. **Ticketing platforms** — Eventbrite, Ticketmaster (reliable, has prices + links)
3. **City guides** — Time Out, local alt-weeklies (good coverage, less structured)
4. **Social/Facebook** — use as a gap-filler, not primary

---

## What to Collect Per Show

For each show found, collect:
- **Show name / performer(s)**
- **Venue name**
- **Date and time**
- **Ticket price** (or "Free" / "PWYW")
- **Ticket/info URL**
- **Short description** (1–2 sentences, if available)
- **Category tag**: `standup`, `improv`, `sketch`, `open-mic`, or `other`

---

## Output Format

Save results to `data/[city-slug]/shows.json`. Create the folder if it doesn't exist.

```json
{
  "city": "philadelphia",
  "week_of": "YYYY-MM-DD",
  "generated_at": "YYYY-MM-DDTHH:MM:SS",
  "show_count": 12,
  "shows": [
    {
      "name": "Show or performer name",
      "venue": "Venue name",
      "date": "YYYY-MM-DD",
      "time": "7:30 PM",
      "price": "$15",
      "url": "https://...",
      "description": "Brief description.",
      "tags": ["standup"]
    }
  ]
}
```

Sort shows chronologically by date, then by time.

---

## Quality Checks

Before saving:
- Remove duplicates (same show listed on multiple sources)
- Confirm all shows fall within the next 6 months from today's date
- Flag any shows missing a URL — these are lower quality and should appear last
- Aim for at least 8–10 shows; note in output if fewer were found

---

## Notes

- If a source is unavailable or returns no results, log it and move on — don't block the workflow.
- "City area" includes the city proper plus well-known nearby venues in the metro area. Use the city config's `region` field for guidance.
- Prefer shows with confirmed dates and ticket links over tentative listings.
- If a city config has no `sources` list yet, use the generic sources above and note that the config should be updated with curated sources after the first successful run.

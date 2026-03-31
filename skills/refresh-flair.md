# Skill: Refresh Flair

Refreshes `data/[city-slug]/flair.json` for a given city by re-checking each named comedian for new press coverage and comedy specials.

Run this every 3 months per city, or whenever a new comedian is added to the city's listings.

---

## Inputs

- City slug (e.g. `philadelphia`)
- `data/[slug]/flair.json` — existing flair data

---

## Steps

### 1. Read the existing flair file

Read `data/[slug]/flair.json`. This is the list of all comedians to check. Iterate over every key that is not prefixed with `_`.

### 2. For each comedian, check for press coverage

Search the web for a **profile piece, feature article, or year-end best-of list** about the comedian in any of these outlets:
- New York Times
- Vulture
- The New Yorker
- The Guardian

**Qualifying coverage:**
- A profile or feature article specifically about the comedian
- Inclusion on a year-end "best of" or "comedians to watch" list

**Not qualifying:**
- Show reviews
- Brief mentions in articles primarily about someone else
- Lists where they appear alongside 20+ others

If qualifying coverage is found that isn't already in `flair.json`, add the outlet name to the comedian's `press` array.

### 3. For each comedian, check for new specials

Search the web and YouTube for a **full-length stand-up special** on **Netflix, HBO Max, Hulu, or YouTube** released in the **last 3 years**.

**YouTube-specific rules:** A YouTube special qualifies only if it is:
- At least 25 minutes long
- Clearly titled or described as a "special" — not an interview, clip, crowd work set, or talk show appearance

If a qualifying special is found:
- If it's newer than what's currently in `flair.json`, replace the existing `special` entry
- If no special exists yet, add it
- Always keep only the most recent qualifying special

Special format:
```json
{ "platform": "Netflix", "title": "Special Title", "year": 2025 }
{ "platform": "YouTube", "title": "Special Title", "year": 2025, "youtube_id": "abc123" }
```

### 4. Update flair.json

- Update the `_last_refreshed` date to today
- Write the updated file back to `data/[slug]/flair.json`
- Note any changes made (new press, updated specials, entries added or removed)

### 5. Rebuild the site

After updating `flair.json`, run the update-site workflow for the affected city so the HTML reflects the latest flair data.

---

## Notes

- Flair is per-comedian, not per-show. If a comedian appears in multiple shows, their flair applies to all of them.
- Do not remove existing press entries unless you can confirm the article no longer exists or was misclassified.
- Do not remove special entries unless the special has been taken down from the platform or is now outside the 3-year window.
- If a comedian no longer appears in any city's listings, leave their flair entry in place — they may return.
- Add `_notes` fields to document any edge cases or judgment calls (e.g. why a special doesn't qualify).

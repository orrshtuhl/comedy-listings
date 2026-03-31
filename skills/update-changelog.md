# Skill: Update the Version History Page

## Purpose

Check for GitHub commits made today, and if any exist, add a single summarized entry to `docs/changelog/index.html`. Run once at end of day. If there are no commits today, do nothing.

---

## When to Run

Run automatically every evening (see `skills/schedule.md`). Can also be triggered manually.

---

## Step 1 — Check for Today's Commits

Run the following to get all commits made today (using today's date):

```bash
git log --oneline --after="YYYY-MM-DD 00:00:00" --before="YYYY-MM-DD 23:59:59"
```

Replace `YYYY-MM-DD` with today's date. If the output is empty, **stop here — do nothing**.

---

## Step 2 — Summarize the Day's Commits

If one or more commits were found:
- Read all commit messages for the day
- Write a single plain-language summary of what changed, as if writing a brief changelog entry
- Keep it to 1–2 sentences
- Use lowercase prose style, consistent with the rest of the site (e.g. "added new cities section to homepage; fixed broken venue links in philadelphia listings")
- Do not mention commit hashes, author names, or git internals
- If only one commit, summarize it directly; if multiple, synthesize them into one coherent entry

---

## Step 3 — Update the Changelog Page

Open `docs/changelog/index.html`. Find the block between:

```html
<!-- CHANGELOG_ENTRIES_START -->
```
and
```html
<!-- CHANGELOG_ENTRIES_END -->
```

**Check if today's date already has an entry** (look for `<div class="entry-date">` matching today's date). If it does, **replace** that entry's `<p class="entry-summary">` with the new summary. If it doesn't, **prepend** a new entry block inside the markers (newest entries at the top):

```html
<div class="entry">
  <div class="entry-date">Month DD, YYYY</div>
  <p class="entry-summary">[your summary here]</p>
</div>
```

Also remove the `<div class="empty-state">` placeholder if it's still present.

Format the date as: `March 30, 2026` (no leading zero on day).

---

## Step 4 — Save

Write the updated `docs/changelog/index.html` back to disk.

No archiving needed for this file — it is itself the running history.

---

## Output

- `docs/changelog/index.html` — updated with one new entry (or one updated entry) for today

---

## Notes

- Maximum one entry per day, no matter how many commits were made.
- Do not edit changelog entries for past dates unless explicitly asked.
- Keep the changelog page visually consistent with the rest of the site (same font, colors, layout).
- This skill does not push to GitHub — that remains a manual step.

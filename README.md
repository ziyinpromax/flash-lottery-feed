# Flâsh ထီ — results feed

Auto-update source for the **Flâsh ထီ** app (Thai Government Lottery
results browser + ticket checker, package `com.parami.flashlottery`).

The app fetches
`https://ziyinpromax.github.io/flash-lottery-feed/draws.json`
on every launch (plain fetch, ~8 s timeout, no custom headers).
The feed is validated strictly before the app trusts it, merged with the
draws bundled in the app (feed wins on a date clash, newest 12 kept),
and saved on the phone — so the app still works offline after the first
successful fetch. If the feed is unreachable, invalid, or the phone is
offline, the app silently keeps using its bundled draws.

## `draws.json` format

```json
{ "draws": [ { "date": "2026-11-16", "first": "645654",
  "adjacent": ["645653", "645655"],
  "second": ["071470", "..."], "third": ["..."], "fourth": ["..."],
  "fifth": ["..."], "front3": ["938", "101"], "last3": ["106", "782"],
  "last2": "06" } ] }
```

Per draw, counts must be exactly: 1st x1, adjacent x2, 2nd x5, 3rd x10,
4th x50, 5th x100, front-3 x2, last-3 x2, last-2 x1 = 173 numbers.
All 6-digit tiers are 6-digit strings; front-3 / last-3 are 3-digit
strings; last-2 is a 2-digit string. **The whole feed is rejected if any
single draw fails validation** — so check counts before pushing.

## How to update after each draw (1st & 16th, results ~16:00 Bangkok)

1. Get the official results (GLO / thairesults.com / thethaiger.com —
   all sources must agree on the headline numbers).
2. Add the new draw object at the **top** of the `draws` array in
   `draws.json` (newest first). Keep the older draws; the app caps the
   merged list at the newest 12.
3. Commit + push to `main`. GitHub Pages republishes the file
   automatically (usually within a minute or two).
4. Verify: `curl https://ziyinpromax.github.io/flash-lottery-feed/draws.json`
   must show the new draw first. Open the app (online) — the new draw
   appears on the home card with no app update.

The app never writes to this feed; it only reads it. No prediction,
no account, no payout — results only.

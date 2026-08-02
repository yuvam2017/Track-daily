# Ghar Tracker (simple, offline version)

A simple, mobile-friendly web app for tracking recurring household items — milk, newspaper, water cans, eggs, or anything else you get regularly. Track daily quantities, adjust for one-off days, handle rate changes over time, and see your monthly bill.

Just 3 static files. No backend, no build step, no account — everything is saved in your browser's local storage on this device.

## Features

- Simple passcode lock (set on first use — a local lock, not a real account)
- Add, edit, delete items — each with a name, unit, default quantity, and rate
- Default quantity carries forward automatically every day until changed
- Change the default quantity or rate from a specific date onward — past days keep their old values
- Override a single day without touching the default (e.g. "0 L, no delivery today")
- Automatic monthly quantity and bill calculation
- Daily history per item, expandable by month
- Export any month to CSV
- Clean, responsive UI — built mobile-first with a bottom nav

## Files

```
index.html   Page structure and lock screen
style.css     All styling
index.js      App logic (storage, calculations, rendering)
```

## Setup

There isn't any. Just open `index.html` in a browser — on your phone, or on a computer. You can also drop the folder on any static host (GitHub Pages, Netlify, etc.) if you want a shareable link, though the data still only lives on whichever device/browser opens it.

On first open, you'll be asked to set a passcode. This just locks the app screen on this device — it isn't a real login, and there's no way to recover a forgotten passcode except clearing your browser's site data (which also erases your items).

## How it works

### Adding an item
Go to the **Items** tab → "+ Add new item". Set its name, unit (L, kg, piece, packet...), default quantity, and rate. This becomes today's starting point.

### Daily use
The **Today** tab shows one card per item with today's quantity already filled in from the default.
- Tap the round **✓/✕** button to mark it taken or skipped for today.
- Tap **✎** to set a custom quantity just for today (e.g. half a packet), without changing the default.

### Changing the default going forward
In the **Items** tab, use "Change quantity" or "Change rate" and pick an effective date. Everything before that date still calculates with the old value — only the new date onward uses the new one.

### History and export
The **History** tab shows month totals per item, expandable into a daily breakdown, plus a button to export the month as CSV.

## Data model

Everything lives under one key in `localStorage` (`ghar-tracker-data-v1`):

```json
{
  "items": [
    {
      "id": "id123abc",
      "name": "Milk",
      "unit": "L",
      "icon": "🥛",
      "createdAt": "2026-08-01",
      "qtyHistory":  [ { "from": "2026-08-01", "val": 1 }, { "from": "2026-08-15", "val": 1.5 } ],
      "rateHistory": [ { "from": "2026-08-01", "val": 60 } ],
      "overrides":   { "2026-08-10": 0 }
    }
  ]
}
```

Only *changes* are stored — a new quantity or rate adds one entry to its history array, and a one-off day adds one key to `overrides`. There's no daily record created for every single day, so the data stays small no matter how long you use the app.

## Adding new item types

No code changes needed — "item type" isn't hardcoded anywhere. Any name, unit, and rate can be entered through "+ Add new item" in the Items tab, so newspapers, water cans, eggs, or anything new all work the same way.

## Limitations

- Data is tied to one browser on one device. Clearing browser data, switching browsers, or switching phones loses it.
- No real backup — export to CSV from the History tab periodically if you want a copy outside the app.
- If you ever want your data synced across devices with real login, there's a Firebase version of this same app that swaps local storage for Firebase Auth + Firestore, keeping the same UI and data model.

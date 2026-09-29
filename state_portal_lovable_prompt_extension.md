# Lovable Extension Prompt — Login Flow + Medalists/Athletes-by-Sport

Paste this into the **same Lovable project** you already built the State Athlete Data Portal in. Do not regenerate the existing Global View or State View — extend them in place, matching the current visual style exactly (same accent color, card style, spacing, type scale, fonts). This is additive scope on top of what's already built and working.

---

## 1. Login flow (new)

**On the existing Global View:**
- Add a **"Log In"** button to the top-right of the header (next to/near the existing nav), routing to a new `/login` page.

**New `/login` page:**
- Centered auth card, styled consistently with the rest of the app (not a generic grey admin-login look).
- Fields: **Username/Email** and **Password**, primary "Log In" button.
- Below the form, a dashed-border "Demo Credentials" callout box showing the actual dummy credentials to use (visible on screen, so it can be read out during a live demo):
  - Username: `meghalaya@atom.demo`
  - Password: `Meghalaya@2027`
- On submit: simple client-side check against that one hardcoded pair (no real backend). Correct → route to the existing Meghalaya state dashboard route. Incorrect → inline error message.
- Small "← Back to Global View" link.

**On the existing State View header:**
- Add a small "Logged in as Meghalaya Admin" indicator with a "Log Out" action that returns to the Global View. Keep this lightweight — doesn't need to gate the route for the prototype, just needs to visually complete the loop for the demo.

---

## 2. Extend the existing "National Games History" tab (new content, existing tab)

Keep everything currently in this tab (edition switcher, district map, sport/gender/age breakdown for that edition) — insert the following two new sections **between the edition switcher and the existing district-map/breakdown content**, in this order:

**a. Medal Tally & Top Performers** (headline section, most prominent placement)
- Row of 3 stat cards: Gold / Silver / Bronze counts for this state in the selected edition.
- Below it, a horizontally scrollable row of 4–6 "Top Performer" spotlight cards: circular avatar placeholder, athlete name, sport, district, achievement line (e.g. "Gold · Archery · 70m Recurve").

**b. Athletes by Sport**
- Pick **5 disciplines**: Archery, Football, Boxing, Wrestling, Athletics.
- Render as 5 tabs or an accordion (one per sport). Each panel is a table/list of this state's athletes in that sport for the selected edition: Name, District, Event/Category, Result (Gold / Silver / Bronze / 4th Place / Participated).
- Medalist rows get a small colored medal icon/badge so they stand out against plain "Participated" rows.
- ~6–10 athletes per sport per edition is enough.

Both sections should update when the edition switcher (37th ↔ 38th National Games) changes, same as the existing content in this tab.

---

## 3. Mock data to add

```
NationalGamesEdition (extend existing) {
  ...existing fields,
  medalTally: { gold, silver, bronze },
  topPerformers: Athlete[],       // 4-6 per edition
  sportsFeatured: {
    sportName,
    athletes: Athlete[]           // one block per sport, 5 sports total
  }[]
}

Athlete {
  name, district, sport, event,   // event = specific category, e.g. "70m Recurve", "57kg Freestyle"
  result: "Gold" | "Silver" | "Bronze" | "4th Place" | "Participated"
}
```

Populate this for Meghalaya's 37th and 38th National Games history only — the other states/editions don't need this level of detail for the demo.

---

Do not change the Team Preparation tab, the Global View map/teaser/pitch-panel behavior, or the overall layout system — this is purely additive: a login gate in front of the existing dashboard, and two new sections inside the existing History tab.

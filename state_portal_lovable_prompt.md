# Lovable / Vibecoding Prompt — State Athlete Data Portal (Prototype)

Paste everything below the line into Lovable (or v0/Bolt). It's written as one self-contained brief: product context, both screens, data model, and visual direction. Mock data only — no real backend/auth needed for this pass.

---

## Product context (for the AI's understanding, not literal UI copy)

We are a sports-tech company that is the official technology & data partner for India's National Games. We already run a live "Team Preparation" dashboard for our host-state client (see reference: district-wise athlete map + sport breakdown + overview stats). We're turning that same visualization into a free lead-gen tool we hand to *other* Indian states: a clean portal where a state can see their own athletes bifurcated by district, sport, gender and age. Free tier is view + bulk-upload only — no editing, no operational tooling — that's the upsell wall.

Build a **three-screen prototype**: a national Global View (India map, sales/lead-gen surface), a Login screen, and a State View (single state's data dashboard, the product itself — reached either via the map's teaser panel or via login). Modern, clean, data-dense-but-breathable SaaS aesthetic — think Linear/Vercel dashboard polish, not a government portal. Generous whitespace, soft shadows, rounded cards, a confident type scale, one accent color doing all the work.

This version is for a live client demo, so the login flow needs to actually work end-to-end with visible dummy credentials (no real backend — just a hardcoded credential check client-side), and the historical section needs named athletes, not just aggregate counts, so it reads as real during a walkthrough.

---

## Screen 1 — Global View (`/`)

**Layout:** Full-bleed hero header with product name + tagline on the left and a **Log In** button top-right, then a two-column layout below: large interactive India map on the left (~65% width), a stats/legend rail on the right (~35% width). Stack vertically on mobile.

**Header CTA:** "Log In" button (top-right, always visible) routes to `/login`. This is the state-admin entry point, separate from clicking a state on the map.

**Map behavior:**
- SVG/topojson map of India, states as individually clickable/hoverable paths.
- Color coding:
  - **Active client states** (Goa, Meghalaya, Jharkhand, Delhi) — filled in the brand accent color, subtle glow or checkmark badge, cursor shows they're clickable.
  - **All other states** — filled neutral grey, slightly muted, still clickable but visually "inactive/available."
  - Hover state: gentle lift/highlight + tooltip with state name.
- Click behavior:
  - Clicking an **active** state opens a right-side slide-over panel (or modal) showing that state's **public aggregated teaser only**: Total Athletes, Districts Covered, Sports Covered, as three large stat cards, plus a "View Full Dashboard →" button (can just route to Screen 2 for prototype purposes, ignore real auth).
  - Clicking a **grey/inactive** state opens a panel styled as a sales pitch: headline **"Activate your ecosystem for [State Name]"**, one line of supporting copy, and a **Contact Us** form (Name, Designation, Phone, Email — State field pre-filled and locked) with a primary CTA button.

**Right rail (persistent, not the click panel):**
- A small "India at a Glance" summary card: total athletes tracked across all active states, number of active states, number of districts, number of sports — aggregated only.
- Below it, a simple legend (Active client / Not yet onboarded) and a short list of the 4 active states as clickable chips (shortcut into their teaser panel without using the map).

**Mock data:** invent plausible numbers for Goa, Meghalaya, Jharkhand, Delhi (a few hundred to a couple thousand athletes each, varying district/sport counts). Meghalaya can mirror the real reference numbers (1,619 athletes, 13 districts, 28 sports) since that's our actual flagship client.

---

## Screen 1.5 — Login (`/login`)

Simple, centered auth card on a clean background (echo the same accent color, not a generic grey admin-login look — this should still feel like the product, not a separate system).

- Fields: **Username/Email** and **Password**, primary "Log In" button.
- Directly on the card (small muted text block, e.g. below the form or in a dashed-border "Demo Credentials" callout box): show the actual dummy credentials to use, so the presenter can read them out loud during the demo. Use Meghalaya as the demo state:
  - Username: `meghalaya@atom.demo`
  - Password: `Meghalaya@2027`
- On submit, do a simple client-side check against that one hardcoded credential pair. Correct → route to `/state/meghalaya`. Incorrect → inline error message, no real validation needed beyond that.
- Small "← Back to Global View" link.

---

## Screen 2 — State View (`/state/:stateName`)

This is the private, logged-in dashboard a state admin sees after onboarding. Two entry paths both lead here: (a) logging in via Screen 1.5 with the Meghalaya demo credentials, or (b) clicking "View Full Dashboard" from an active state's teaser panel on the map. Once inside, show a small "Logged in as [State] Admin" indicator near the header with a "Log Out" action that returns to Global View.

**Header:** State name as the page title, small breadcrumb back to Global View, and two tabs:
1. **Team Preparation** (default, active tab) — current cycle, this state's own uploaded data.
2. **National Games History** — view-only. Only render this tab at all for states that have historical participation data (Meghalaya + one other mock "host-history" state qualify; the rest simply don't show this tab — build both states so both cases are visible in the prototype).

**Team Preparation tab layout** (reuse/modernize the reference screenshot's structure, don't copy it 1:1 — elevate it):
- Top row: 3–4 large overview stat cards — Total Athletes, Districts, Sports, and one standout metric (e.g., "Fastest Growing District").
- Main content: two-column layout.
  - **Left (larger):** District map of the state, same interaction pattern as the global map — click a district to filter everything below to that district (sport breakdown, gender/age charts). Include a clear "All Districts" reset chip when filtered.
  - **Right (narrower):** a ranked, scrollable "Districts" list (name + athlete count, sorted descending), and below it a "Sport Breakdown" mini list/dropdown (same pattern as reference: sport name, share, count).
- Below the two-column section, a full-width row with **Gender split** (simple donut or split bar) and **Age group split** (bar chart across age bands, e.g. U14 / 14–17 / 18–23 / 24+) — both filterable by the district/sport selections above, live-updating.
- All charts/lists should visibly respond when a district or sport filter is applied (this is the core "clickable, filterable" interaction the product is sold on — make it feel snappy).
- Empty state: if a state has zero uploaded athletes, show a friendly empty state with a prominent "Upload Athlete Data" CTA (button can be non-functional for the prototype, just needs to exist and look real) instead of blank charts.

**National Games History tab** (where applicable):
- Explicitly view-only visual treatment — no upload buttons, no edit affordances, maybe a small "Read-only · Historical record" badge near the tab header.
- A simple edition switcher (e.g. "37th National Games" / "38th National Games" as tabs or a dropdown).
- Same visual language as Team Preparation (district map + sport breakdown + gender/age), but scoped to that historical edition's participation data for this state only.

- **Medal Tally & Top Performers** (new sub-section, sits right below the overview stat cards for this tab): a row of 3 stat cards — Gold / Silver / Bronze counts for this state in the selected edition — followed by a horizontally scrollable row of "Top Performer" spotlight cards: circular avatar placeholder, athlete name, sport, district, and their achievement (e.g. "Gold · Archery · 70m Recurve"). Pick 4–6 standout athletes per edition to feature here. This section should visually read as the highlight reel — most prominent placement in the tab.

- **Athletes by Sport** (new sub-section, below Medal Tally): pick **5 popular disciplines** for Meghalaya's historical data — Archery, Football, Boxing, Wrestling, Athletics. Render as 5 tabs or an accordion, one per sport. Each sport panel shows a table/list of that state's athletes who competed in that sport in the selected edition: Name, District, Event/Category, Result (e.g. "Gold", "Silver", "4th Place", "Participated"). Medalist rows get a small medal icon/colored badge (gold/silver/bronze) so they stand out at a glance against plain "Participated" rows. ~6–10 athletes per sport is enough for the mock.

---

## Data model (mock, client-side is fine)

```
State {
  name, status: "active" | "inactive",
  totalAthletes, districtsCount, sportsCount,
  districts: District[],
  hasNationalGamesHistory: boolean
}

District {
  name, athleteCount,
  sports: { sportName, count }[],
  genderSplit: { male, female, other },
  ageGroups: { label, count }[]
}

NationalGamesEdition {
  editionName, year, hostState,
  districts: District[],       // same shape, this state's athletes only
  medalTally: { gold, silver, bronze },
  topPerformers: Athlete[],    // 4-6 featured athletes for the spotlight row
  sportsFeatured: {
    sportName,
    athletes: Athlete[]        // one entry per sport, 5 sports total
  }[]
}

Athlete {
  name, district, sport, event,       // event = specific category, e.g. "70m Recurve", "57kg Freestyle"
  result: "Gold" | "Silver" | "Bronze" | "4th Place" | "Participated"
}

DemoCredentials {
  username: "meghalaya@atom.demo",
  password: "Meghalaya@2027",
  routesTo: "/state/meghalaya"
}
```

---

## Visual direction
- One confident accent color (pick a saturated blue, purple, or teal — avoid the source screenshot's multi-color bar-chart segments; use accent + 2–3 neutral tints for chart series instead).
- Card-based composition, soft rounded corners (12–16px), subtle elevation shadows, no heavy borders.
- Modern sans-serif type stack, strong hierarchy between stat numbers (large, bold) and labels (small, muted uppercase or sentence case).
- Micro-interactions: hover lift on map states/districts and cards, smooth filter transitions on charts.
- Fully responsive; charts and maps should gracefully stack on mobile.

---

Build all three screens with working mock-data interactivity (map clicks actually filter/update the dashboard, login actually gates the dashboard route) — this needs to demo convincingly to a state government stakeholder, not just look static. The login flow with visible demo credentials is the opening beat of the demo, so it needs to feel real and be fast to click through.

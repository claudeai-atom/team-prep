# Lovable Extension Prompt — Athlete Listing Table (Team Preparation tab)

Paste this into the **same Lovable project**. Extend the existing **Team Preparation** tab only — don't touch the Global View, Login flow, or the National Games History tab. Match the existing visual style exactly (same accent color, card style, spacing, type scale).

---

## 1. New section: Athlete Listing (bottom of the Team Preparation tab)

Add a new section at the very end of the tab, below the existing Gender Split / Age Group Split row: a **data table** listing individual registered athletes for the state.

**Columns, in this order:**
1. **Sport**
2. **Athlete Name**
3. **Phone Number/Email (Unique ID)**
4. **DOB (Age)** — show date of birth with computed age alongside, e.g. `12 Mar 2008 (17)`
5. **Gender** — Male/Female
6. **District** — location, district-wise

Standard table styling consistent with the rest of the app: header row, zebra or hover row highlighting, comfortable row height, paginated (or virtualized scroll) rather than dumping everything unpaginated. Show a row count summary above the table (e.g. "Showing 42 of 214 athletes").

---

## 2. Filtering — this table must respect every existing click filter, and two filters need to become clickable that currently aren't

Today, clicking a district on the map (and clicking a sport in the sport breakdown list) already filters the dashboard. Extend that same shared filter state so that:
- Clicking a **district** on the map filters the table to that district.
- Clicking a **sport** in the sport breakdown filters the table to that sport.
- Clicking a segment of the **Gender Split** chart filters the table to that gender (this chart is currently just a visual — make its segments clickable filters too).
- Clicking a bar in the **Age Group Split** chart filters the table to that age band (same — make it clickable).

All of these combine (AND logic) — e.g. clicking "East Khasi Hills" then "Archery" then "Female" narrows the table to female archery athletes from East Khasi Hills, and the other charts on the page should also reflect that combined filter (as they already do for district/sport today).

**Active filter bar:** add a small row above the table showing removable chips for every currently active filter (e.g. `District: East Khasi Hills ×`, `Sport: Archery ×`, `Gender: Female ×`), plus a "Clear All" link. This makes the applied filters legible during a live demo instead of just inferred from chart highlighting.

---

## 3. Mock data

Add individual athlete records backing this table — not just the aggregate counts already used for the existing stat cards/charts. Generate a realistic-sized dataset (150–200+ records for Meghalaya, procedurally varied across its districts, sports, genders, and ages) so filters produce non-trivial, believable result counts rather than 2-3 rows. Make the existing "Total Athletes" overview stat equal the count of generated records, so the numbers reconcile cleanly across the page.

```
RegisteredAthlete {
  sport,
  name,
  contactId,       // phone number or email, used as the unique ID
  dob,             // compute age for display, don't hardcode age separately
  gender: "Male" | "Female",
  district
}
```

Derive the existing Sport Breakdown / Gender Split / Age Group Split / District counts from this same athlete list where possible, so the aggregate numbers and the table are always consistent with each other.

---

Do not change anything in the Login flow, Global View, or National Games History tab — this is scoped entirely to adding the filtered athlete table at the bottom of Team Preparation.

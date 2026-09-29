# Lovable Extension Prompt — Add Haryana (Team Preparation + National Games History)

Paste this into the **same Lovable project** used for the State Athlete Data Portal (the one already built for Meghalaya, Goa, Jharkhand, Delhi, with the Global View map, Login, Team Preparation tab, National Games History tab, and the filtered Athlete Listing table). Do not regenerate anything — this adds **Haryana** as a second fully-built active state, at the exact same data depth as Meghalaya, reusing every existing component, layout, and interaction pattern as-is.

---

## 1. Global View — activate Haryana

- Add **Haryana** to the "active client states" list on the India map (alongside Goa, Meghalaya, Jharkhand, Delhi) — same accent-color fill, glow/checkmark badge, clickable teaser panel behavior as the others.
- Clicking Haryana on the map opens the same teaser panel pattern: Total Athletes, Districts Covered, Sports Covered as three stat cards, plus "View Full Dashboard →".
- Add Haryana as a fifth chip in the right-rail "active states" shortcut list.
- Update the "India at a Glance" aggregate card totals to fold in Haryana's numbers.

---

## 2. Login — new demo credential pair

Add a second hardcoded credential pair to the existing client-side check (don't replace the Meghalaya one):

- Username: `haryana@atom.demo`
- Password: `Haryana@2027`
- Correct → route to `/state/haryana`. Same inline-error behavior on mismatch as today.
- Show both credential pairs in the "Demo Credentials" callout box on the login card (Meghalaya and Haryana, clearly labeled) so either can be demoed live.

---

## 3. Team Preparation tab — full build for Haryana (`/state/haryana`)

Reuse the exact layout already built for Meghalaya: overview stat cards, district map (click-to-filter), ranked district list, sport breakdown list, gender split, age group split, and the filtered Athlete Listing table at the bottom — all with the same click-through filter behavior (district → sport → gender → age group all combine, AND logic, with the removable filter-chip bar above the table).

**Districts (22, real Haryana districts — use these for the map + list, not placeholders):**
Ambala, Bhiwani, Charkhi Dadri, Faridabad, Fatehabad, Gurugram, Hisar, Jhajjar, Jind, Kaithal, Karnal, Kurukshetra, Mahendragarh, Nuh, Palwal, Panchkula, Panipat, Rewari, Rohtak, Sirsa, Sonipat, Yamunanagar.

**Overview stats (mock, plausible for Haryana's scale):**
- Total Athletes: ~2,450
- Districts: 22
- Sports: 20
- Standout metric card: "Fastest Growing District" — e.g. "Sonipat +18% this cycle"

**Sport breakdown (mock counts, ranked descending — lean into Haryana's real sporting strengths so the demo reads credibly):**
Wrestling, Boxing, Athletics, Hockey, Kabaddi, Judo, Weightlifting, Shooting, Archery, Cycling, Kho-Kho, Handball, Volleyball, Basketball, Football, Table Tennis, Badminton, Swimming, Taekwondo, Wushu — invent a descending count per sport (largest ~260, smallest ~40), same visual pattern (stacked segment bar) as Meghalaya's list.

**Gender split / Age group split:** same donut/bar pattern, filterable, same age bands (U14 / 14–17 / 18–23 / 24+).

**Athlete Listing table (bottom of tab):** same columns as Meghalaya's (Sport, Athlete Name, Phone/Email as unique ID, DOB (Age), Gender, District), same pagination, same row-count summary, same combined filtering. Generate **250–300 mock `RegisteredAthlete` records** for Haryana (larger than Meghalaya's set, matching the bigger overview total), procedurally varied across the 22 districts, 20 sports, both genders, and all four age bands. Make Total Athletes on the overview card equal the generated record count, and derive Sport Breakdown / Gender Split / Age Group Split / District counts from this same list so all numbers reconcile.

---

## 4. National Games History tab — full build for Haryana

Same tab structure as Meghalaya: view-only badge, edition switcher (**37th National Games** / **38th National Games**), and in this order: Medal Tally & Top Performers, then Athletes by Sport, then the district-map/breakdown section scoped to that edition.

**Medal Tally & Top Performers:**
- Per edition, invent a strong Gold/Silver/Bronze stat-card tally reflecting Haryana's real-world reputation as one of India's top-performing states at multi-discipline games (skew meaningfully higher than Meghalaya's mock tally).
- 4–6 Top Performer spotlight cards per edition: avatar placeholder, athlete name, sport, district, achievement line (e.g. "Gold · Wrestling · 65kg Freestyle").

**Athletes by Sport:**
- 5 disciplines, matching Haryana's signature strengths: **Wrestling, Boxing, Athletics, Hockey, Kabaddi**.
- Same 5-tab/accordion pattern, each panel a table: Name, District, Event/Category, Result (Gold / Silver / Bronze / 4th Place / Participated), with the same medal-badge treatment on medalist rows.
- ~6–10 athletes per sport per edition.

Both sections update live with the edition switcher, same as Meghalaya's.

---

## 5. Mock data to add

```
State {
  name: "Haryana", status: "active",
  totalAthletes: ~2450, districtsCount: 22, sportsCount: 20,
  districts: District[],   // 22 real districts listed above
  hasNationalGamesHistory: true
}

RegisteredAthlete {
  sport, name, contactId, dob, gender: "Male" | "Female", district
}
// 250-300 records for Haryana

NationalGamesEdition (Haryana-specific instances for 37th & 38th) {
  editionName, year, hostState,
  districts: District[],
  medalTally: { gold, silver, bronze },   // skew higher than Meghalaya
  topPerformers: Athlete[],               // 4-6 per edition
  sportsFeatured: [
    { sportName: "Wrestling", athletes: Athlete[] },
    { sportName: "Boxing", athletes: Athlete[] },
    { sportName: "Athletics", athletes: Athlete[] },
    { sportName: "Hockey", athletes: Athlete[] },
    { sportName: "Kabaddi", athletes: Athlete[] }
  ]
}

DemoCredentials (add second pair) {
  username: "haryana@atom.demo",
  password: "Haryana@2027",
  routesTo: "/state/haryana"
}
```

---

Do not change Meghalaya's, Goa's, Jharkhand's, or Delhi's data, layout, or the shared component styling — this is purely additive: one more fully-populated state, built to the same end-to-end depth (Team Preparation + National Games History + Athlete Listing) as Meghalaya, using the identical visual language and interaction model already in place.

# Khelo Atlas: State Public Page, Lovable prompt (2-slice, credit-optimized)

Date: 2026-09-26. Paste Slice 1, check it in the preview, then paste Slice 2.
Single-paste alternative (same content): `state_portal_lovable_prompt_extension_public_page.md`.
Requirements/decisions: memory note `gms_state_public_page_requirements.md`.
Still open for PRD: pathway upper-tier data sources, privacy wording ("aggregates + consented tagged achievers"), whether suppression threshold 5 stays.

---

## Slice 1: Global View + State Admin tab

```
Extend this project, additive only. Reuse existing components and tokens (accent colour, cards, stat cards, ranked lists, badges, district map, sport filter). No new visual style.

DO NOT TOUCH: /login and its 3 roles; State View tabs (Team Preparation, National Games History, Team & Trials); Directorate/Federation/Stakeholder dashboards; Master Admin; National Map; existing public routes /state/:stateName/grants|achievers|associations (only add a "← Back to public page" link to them); grey-state Contact Us panel; existing mock data. Derive every number from existing RegisteredAthlete, Squad, Achiever, BudgetPool, GrantRequest, Tranche records.

1. GLOBAL VIEW (`/`)
- Rename the page "Khelo Atlas" (header wordmark + browser tab title), with subheading above the map: "Every state. Every athlete pathway. In the open."
- Keep the map and grey-state Contact Us as is.
- Replace the bottom active-state chips with a right-side panel (stacks under the map on mobile): 4 stat tiles (Onboarded States, Total Athletes, Sports, Districts, summed over active states) above a scrollable list of onboarded states (colour dot, name, athlete count, sorted desc). Row hover and map hover highlight each other; row click = map click.
- In the active-state slide-over keep the 3 stat cards and add "Top sport". REPLACE "View Full Dashboard" with primary "View Public Page →" to `/p/:slug` (disabled + tooltip "Public page not published yet" if unpublished). Add a small link "State admin? Log in" → `/login`. The teaser must no longer lead to the private dashboard.

2. STATE ADMIN: 4th tab "Public Page" in `/state/:stateName`
- URL chip `/p/[slug]` + Copy. Slug = auto-generated kebab-case state name, unique, read-only.
- Status pill + "Published" switch (default on).
- "Preview page" button → `/p/:slug?preview=1` in a new tab.
- Toggles for what the public sees, instant, with a "Saved" cue: District map & overview, Sport breakdown, Athlete pathway, Grant scheme, Programme at a glance, Success stories. These are the ONLY controls for the public page; Directorate "Permissions & Display" flags still govern only the existing /grants and /achievers pages.
- Page details: logo + hero upload placeholders, `publicIntro` textarea.

3. MOCK DATA (additive)
State: slug, publicPublished:true, publicIntro, publicSections {districtMap, sportBreakdown, pathway, grants, programme, successStories: all true}.
```

---

## Slice 2: Public page `/p/:slug`

```
Add the public page `/p/:slug` (no login). Same DO-NOT-TOUCH list and reuse rules as before. Unknown slug, or unpublished and not in preview: show "This page is not available" + link to `/`. `?preview=1` works even when unpublished and shows a sticky banner "Preview: this is how your public page looks [status]" with "Back to editor" (State Admin Public Page tab) and "Copy public link".

LAYOUT mirrors the "Team Preparation" National Games reference: floating rounded nav pill (logo; "Khelo Atlas" → `/`; About; Team Preparation; Grants; Success Stories, only for visible sections; Share = copy URL + toast; Log In → `/login`); large hero photo with dark overlay and giant white "Team Preparation" + state subtitle; off-white background; big condensed headings; rounded white soft-shadow cards; pastel district colours, same district = same colour everywhere. Aggregate-only except Success Stories. Render only sections whose flag is on, in this order:

1. Athlete Representation By District: heading + `publicIntro`. Row: LEFT district choropleth (hover tooltip), RIGHT "Overview" card (highlighted Total Athletes tile, Districts, Sports, scrollable "All District" ranked list).
2. Sport Breakdown. Row: LEFT horizontal stacked bars per sport (segments = districts, total at end), RIGHT card (All Sports dropdown, big total, district mini-list with dots). Clicking a district filters the bars; picking a sport recolours the map.
3. Athlete Pathway: funnel/pyramid, widest at the bottom: Total Athletes > In Preparation (unique athletes in any Squad) > Participating in Events > Selected for National. Show count, % of total, and conversion % between tiers. Blue sequential ramp.
4. Grant Scheme: year chips; 3 KPI tiles (Total Budget, Allocated, Disbursed); DONUT of allocation by association (total in the centre, legend with amount and %); concentric RADIAL PROGRESS RINGS per association (Disbursed arc vs Allocated ring, % in centre). Hover tooltips. Click a segment, ring or legend item → `/state/:stateName/associations`; link "View full grant dashboard →" → `/state/:stateName/grants`. Approved grants only, never pending amounts.
5. Programme at a Glance: stat cards Coaches and Training Centres; donut Gender split; column chart Age groups.
6. Success Stories: athletes tagged as Achievers by State Admin, Directorate OR any Association (not only Featured). Featured first, then newest first, max 8. Card: photo, name, sport, district, level badge, achievement, year, chip "Tagged by: State/Directorate/[Association]". Sport + Level filter chips. Button "View All Achievers" → `/state/:stateName/achievers`.
7. Footer: "Aggregated data; individual records shown only for consented achievers", URL chip with copy, "Powered by Khelo Atlas".

PRIVACY: const SUPPRESS_BELOW = 5 (single constant). Only cross-breakdown cells (district×sport segments, Gender, Age) with a value <5 render "<5" (thin neutral sliver + tooltip "Small numbers hidden for privacy"). Never suppress totals, district/sport totals, pathway tiers, grant amounts or Success Stories.

MOCK DATA: per-state seeded pathwayCounts for Events and National (placeholder, strictly decreasing below In Preparation), coaches, trainingCentres. Gender/Age from RegisteredAthlete if present, else proportional seeds summing to the total.
```

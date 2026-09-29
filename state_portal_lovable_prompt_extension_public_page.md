# Lovable prompt: State Public Page + Global View wiring (consolidated, credit-optimized)

Single paste, replaces the earlier two-part draft (base + revision). Paste into the SAME Lovable project.
Written tight on purpose: one pass, no repeated context, no separate acceptance-test essay (Lovable spends credits re-reading long prompts and "fixing" things it was told not to touch).

Open items still to confirm for the PRD: pathway upper-tier sources, exact privacy wording ("aggregates + consented tagged achievers"), threshold owner.

---

```
Extend this project. Additive only. Reuse existing components/tokens (accent colour, cards, stat cards, ranked lists, badges, district map, sport filter, Achiever cards). No new visual style.

## DO NOT TOUCH
/login and its 3 roles; State View tabs (Team Preparation, National Games History, Team & Trials); Directorate/Federation/Stakeholder dashboards; Master Admin panel; National Map; existing public routes /state/:stateName/grants|achievers|associations (keep, only add a "← Back to public page" link to them); Contact Us panel for grey states; all existing mock data. Derive every number from existing RegisteredAthlete, Squad, Achiever, BudgetPool, GrantRequest, Tranche records.

## 1. Global View (`/`)
- Branding: this page is now called "Khelo Atlas". Show "Khelo Atlas" as the page title/wordmark in the header (and browser tab title) with the tagline "Every state. Every athlete pathway. In the open." as the subheading above the map. Keep existing layout otherwise. On `/p/:slug`, footer reads "Powered by Khelo Atlas", and the "Home" nav item is labelled "Khelo Atlas" (→ `/`).
- Keep map + grey-state Contact Us as is.
- Replace the bottom active-state chips with a right-side panel (stacks under map on mobile): top = 4 stat tiles (Onboarded States, Total Athletes, Sports, Districts, summed over active states); below = scrollable list of onboarded states (colour dot, name, athlete count, sorted desc). Row hover <-> map hover highlight; row click = map click.
- Slide-over for active state: keep 3 stat cards, add "Top sport". REPLACE "View Full Dashboard" with primary "View Public Page →" to `/p/:slug` (disabled with tooltip if page unpublished). Add small link "State admin? Log in" to `/login`. The teaser must no longer lead to the private dashboard.

## 2. Public page `/p/:slug` (no login)
Slug = auto-generated kebab-case state name, unique, read-only. `?preview=1` = preview mode. Unknown slug or unpublished (non-preview) = "This page is not available" + link to `/`.
Layout mirrors the reference (National Games "Team Preparation"): floating rounded nav pill (logo; Home→`/`, About, Team Preparation, Grants, Success Stories, only for visible sections; Share = copy URL + toast; Log In→`/login`), large hero photo with dark overlay and giant white "Team Preparation" + state subtitle, off-white background, big condensed headings, rounded white soft-shadow cards, pastel district colours (same district = same colour everywhere). Aggregate-only except Success Stories. Render only sections whose flag is on, in this order:
1. Athlete Representation By District: heading + intro (`publicIntro`); row: LEFT district choropleth map (hover tooltip), RIGHT "Overview" card (highlighted Total Athletes tile, Districts, Sports, scrollable "All District" ranked list).
2. Sport Breakdown: row: LEFT horizontal stacked bars per sport (segments = districts, total at end), RIGHT card (All Sports dropdown, big total, district mini-list with dots). District click filters bars; sport pick recolours map.
3. Athlete Pathway: funnel/pyramid, widest bottom: Total Athletes > In Preparation (unique athletes in any Squad) > Participating in Events > Selected for National; count, % of total, conversion % between tiers.
4. Grant Scheme: year chips; 3 KPI tiles (Total Budget, Allocated, Disbursed); DONUT of allocation by association (total in centre, legend with amount/%); concentric RADIAL PROGRESS RINGS per association (Disbursed arc vs Allocated ring, % centre). Tooltips on hover. Click segment/ring/legend → `/state/:stateName/associations`; link "View full grant dashboard →" → `/state/:stateName/grants`. Approved grants only, never pending amounts.
5. Programme at a Glance: stat cards Coaches, Training Centres; donut Gender split; column chart Age groups.
6. Success Stories: athletes tagged as Achievers by State Admin, Directorate OR any Association (not only Featured). Featured first, then newest first, max 8. Card: photo, name, sport, district, level badge, achievement, year, chip "Tagged by: State/Directorate/[Association]". Sport + Level filter chips. Button "View All Achievers" → `/state/:stateName/achievers`. Default on.
7. Footer: data-source note ("Aggregated; individual records not shown except consented achievers"), URL chip with copy, "Powered by Khelo Tech".
Privacy: const SUPPRESS_BELOW = 5. Only cross-breakdown cells (district×sport segments, Gender, Age) with value <5 render "<5" (thin neutral sliver + tooltip "Small numbers hidden for privacy"). Never suppress totals, district/sport totals, pathway tiers, grant amounts, Success Stories.

## 3. State Admin: new 4th tab "Public Page" in `/state/:stateName`
- URL chip `/p/[slug]` + Copy; status pill Published/Unpublished with a Published switch (default on).
- "Preview page" button → `/p/:slug?preview=1` in new tab. Preview shows sticky banner "Preview: this is how your public page looks [status]" with "Back to editor" and "Copy public link"; works while unpublished, no login needed (frontend demo).
- "What the public sees" toggles, live/instant with "Saved" cue: District map & overview, Sport breakdown, Athlete pathway, Grant scheme, Programme at a glance, Success stories. These are the ONLY controls for the public page; the Directorate "Permissions & Display" flags keep controlling only the existing /grants and /achievers pages.
- Page details: logo + hero image upload placeholders, `publicIntro` textarea; live-updates preview.

## 4. Mock data (additive)
State: slug, publicPublished:true, publicIntro, publicSections {districtMap,sportBreakdown,pathway,grants,programme,successStories: all true}. Per-state seeded pathwayCounts for Events and National (placeholder, strictly decreasing below In Preparation), coaches, trainingCentres. Gender/Age from RegisteredAthlete if present, else proportional seeds summing to total.
```

---

## Credit-saving rules I'm applying to future Lovable prompts
1. One consolidated paste, not base + revision; merge overrides into the original.
2. State the DO-NOT-TOUCH list once, briefly; no repeated reminders per section.
3. Point to existing components ("reuse X") instead of describing them again.
4. Drop acceptance-test essays; Lovable does not run them. Verify manually in preview instead.
5. Specify data derivation rules once ("derive from existing records") rather than per screen.
6. Ship in slices when a build is big (e.g. Global View + State Admin tab first, then public page sections), so a wrong assumption costs one small rebuild, not a full one.

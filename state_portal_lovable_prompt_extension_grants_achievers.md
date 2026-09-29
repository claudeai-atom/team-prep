# Nomenclature & assumptions

- **Directorate** = **a distinct account, provisioned by State Admin** (not the same login wearing a different hat — corrected from an earlier pass of this doc). State Admin creates it and issues its credentials, the same way Master Admin activates a State — see the companion doc `state_portal_lovable_prompt_extension_national_rbac.md` for that creation flow. State Admin keeps its existing Team Preparation duties untouched; Directorate is the separate operational login for budget/recognition governance.
- **Federation** in this doc always means **State Federation** — a single sport's governing body *within one state* (e.g. "Meghalaya Archery Association"), sport-scoped, provisioned by that state's State Admin, with write access (request grants, tag achievers, upload roster). This is distinct from a **National Federation** (e.g. "AIFF") — a sport's governing body *across all states*, read-only, provisioned by Master Admin or a National Director — which is covered entirely in the companion national/RBAC doc, not here.
- **Budget Pool** = the amount Directorate releases for a funding cycle (state-wide, not pre-split by sport).
- **Grant Request** = a Federation's ask against an open Budget Pool.
- **Disbursement / Tranche** = one dated, tracked release of money against an *approved* Grant Request. A Grant Request can have many tranches (phased release).
- **Achiever** = **not a new entity** — it's a *tag* on an athlete who already exists in the central roster (`RegisteredAthlete`, the same record powering the Athlete Listing table and Squads). Marking someone an achiever means picking them from the existing roster and layering achievement-specific info on top (Level, achievement title, year, photo) — never re-typing their name/sport/district by hand. Sport, district and Federation are inherited from the underlying athlete record, not re-entered.
- This pass is **frontend-only with dummy credentials**, same as the rest of this prototype — no real backend, no real RBAC engine. The full resource × scope × detail-level permission grid (same pattern as the Federation/Director access model already designed for Team Preparation) is a **later backend concern**, not built here. This prompt only needs three login personas + a public layer to demo the story convincingly.

---

# Lovable Extension Prompt — Grants, Disbursement & Achievers

Paste this into the **same Lovable project**. Match the existing visual style exactly (same accent color, card style, spacing, type scale, map/filter interaction patterns) and reuse existing components (stat cards, ranked lists, badges, the Athlete Listing table pattern) wherever noted. Do not touch the existing Global View, Login flow's existing State Admin path, Team Preparation tab, National Games History tab, or Team & Trials tab — this is purely additive.

---

## 1. Login — add a role selector

Extend the existing `/login` screen with a simple **role tab/selector** above the form: **State Admin** (existing, unchanged) | **Directorate** | **Federation**. Selecting a role just swaps which dummy credential pair is checked on submit and where it routes.

Add two new dummy credential pairs, shown in the same "Demo Credentials" callout box style as the existing one, updating live as the role tab changes:

- **Directorate** — `directorate.meghalaya@atom.demo` / `Directorate@2027` → routes to `/state/meghalaya/directorate`
- **Federation** — `archery.meghalaya@atom.demo` / `Federation@2027` → routes to `/state/meghalaya/federation` (seed the demo federation as **Meghalaya Archery Association**)

Same client-side hardcoded check as the existing login, same inline-error-on-mismatch behavior, same "← Back to Global View" link.

---

## 2. Directorate Dashboard (`/state/:stateName/directorate`)

Header: "Logged in as Directorate — [State Name]" with Log Out. Four sections/tabs on this dashboard:

**2a. Budget Pool**
- Top: a stat-card row — Total Budget Released (this cycle), Total Disbursed, Total Pending Requests, Federations Funded.
- "+ Release New Budget Pool" button opens a small form: amount, cycle/label (e.g. "FY 2026-27"), optional note. Creates a new `BudgetPool` in **Open** status. Seed one already-open pool so the dashboard isn't empty on load.

**2b. Grant Requests (incoming)**
- A table/list of Grant Requests from Federations against the open pool: Federation name, sport, requested amount, utilization plan (short text), status badge (**Pending** / **Approved** / **Partially Approved** / **Rejected**), submitted date.
- Clicking a row opens a **Review** panel:
  - Shows the request detail + Federation's utilization plan text.
  - Directorate enters a **sanctioned amount** (can be less than, equal to, or — block — greater than requested) and picks Approve (full or partial, whichever the amount implies) or Reject (with optional reason).
  - On approval, Directorate can immediately schedule the release as **one lump disbursement** or **multiple phased tranches** — a small repeatable row: amount + planned date, "+ Add Tranche" to add more, running total must not exceed the sanctioned amount (show a live remaining-to-allocate counter).
  - Confirming creates the tranche schedule in **Scheduled** status.

**2c. Disbursement Tracker**
- One row per scheduled tranche across all approved requests: Federation, sport, tranche amount, planned date, status (**Scheduled** / **Released**), and a "Mark as Released" action (sets status + records an actual release date, defaults to today, editable). This is the operational log — Directorate's view of "what's gone out and when."

**2d. Achievers (management)**
- Reuse the same card-grid pattern as the public Achievers section (below), but with Add/Edit/Delete controls.
- "**+ Tag Achiever**" (not "Add" — it's a tag on an existing record, keep the label honest) opens a **two-step flow**:
  1. **Pick the athlete** — reuse the existing Athlete Listing table/picker exactly as built for Squad creation (same columns, same district/sport/gender/age filters, same search), single-select this time instead of multi-select checkboxes. This is the entire point: no free-text name/sport entry, the athlete must already exist in the central roster.
  2. **Add achievement info** — once an athlete is picked, a small form appended below their selected row: Level (dropdown: National / International / State / + "＋ Add Category" inline to create a new custom level tag on the fly, persisted for reuse), Achievement/title text (e.g. "Gold — U19 National Championship"), Year, photo upload/placeholder (this is achievement-specific — it's not stored back onto the athlete's core roster record, just onto this tag).
- Sport, district and Federation shown on the resulting Achiever card are **read straight from the picked athlete's roster record**, not re-entered.
- Directorate can tag/edit/remove any achiever in the state, regardless of which Federation added the tag (Federation can only manage their own — see §3). Re-tagging the same athlete a second time (e.g. a new achievement in a later year) is allowed — one athlete can carry multiple Achiever tags over time, shown as multiple cards or a stacked history on one card (either is fine, pick whichever reads cleaner).

**2e. Permissions & Display**

This is Directorate's admin control over what each Federation can do, and what the public portal shows — the demo-scaled expression of the same "permissions are primitives, scope is granted not earned" philosophy already used for RBAC elsewhere in the product (Team Preparation's stakeholder access grants, BMS/Ticketing RBAC). This isn't the full resource × scope × detail-level grant engine — just the two controls this prototype actually needs to demo convincingly.

**i. Federation Permissions** — a table, one row per Federation, with toggle-switch columns:
- **Can Submit Grant Requests** — on by default. Toggling off disables the "+ Request Grant" button on that federation's dashboard (§3c), with a tooltip explaining why, instead of hiding the section entirely.
- **Can Tag Achievers** — on by default. Toggling off disables "+ Tag Achiever" on that federation's dashboard (§3e) the same way.
- **Listed on Public Associations Directory** — on by default. Toggling off removes that federation's card from the public Associations page (§4c) — useful for a federation that's inactive or not yet verified.

Changes here must actually take effect live on the corresponding Federation login and public pages — this is the whole point of the control, so it needs to demo as cause → effect, not just save silently.

**ii. Frontend Display Components** — toggle-switch list controlling which public tabs render for this state's portal nav (§4): **Grant Sanction Dashboard**, **Achievers**, **Associations**. All on by default. Turning one off removes that pill from the public nav and makes the route show a simple "Not available for this state" state instead of a 404. Below the toggles, a **Featured Achievers** picker: a short reorderable list (star/pin icon) letting Directorate mark up to 4–6 achievers as "Featured" — these render first/highlighted on the public Achievers page (§4b), same spotlight treatment as the existing National Games History "Top Performers" row.

---

## 3. Federation Dashboard (`/state/:stateName/federation`) — this is the centerpiece screen for the demo

Header: "Logged in as [Federation Name] — [State Name]" with Log Out. This is the persuasion surface, so it should read as genuinely useful to a federation, not just a form.

**3a. Overview row** — stat cards: Total Sanctioned (all-time), Total Received (disbursed-to-date), Pending Request status (if one is open), Achievers Featured count.

**3b. Manage Our Roster** — federations run their own ecosystem, which includes contributing athletes, not just picking from a state-uploaded list.
- Reuse the exact same bulk-upload flow already built for State Admin's Team Preparation tab (template download → fill → upload → validate against the district list), scoped so every athlete added here is automatically tagged with this federation's sport and stamped `addedBy: "Federation"`. Uploaded rows append to the same central `RegisteredAthlete` list used everywhere else (Athlete Listing table, Squads, Achiever tagging) — this is additive to the central repo, not a separate federation-only list.
- Below the upload CTA, the same Athlete Listing table/filter component, pre-filtered to this federation's sport (removable filter, same pattern as elsewhere), so a federation can see and manage exactly the athletes attributed to them regardless of who originally uploaded them (state bulk-upload or federation upload).

**3c. Request Budget**
- "+ Request Grant" button → form: amount requested, **utilization plan** (textarea — what the money will be used for, e.g. equipment/coaching camp/travel, with a rough breakdown), optional supporting note. Submits as a new Grant Request in **Pending** status against the currently open Budget Pool. Disable the button (with a tooltip) if there's already a Pending request outstanding — one open request at a time per federation is the assumed rule for this prototype.
- Below: a **history list** of this federation's past requests — requested amount, sanctioned amount (if decided), status badge (Pending / Approved / Partially Approved / Rejected).

**3d. Disbursement Tracker** — this is the direct mirror of Directorate's §2c, scoped to this federation only, and it's the payoff moment of the whole flow: money Directorate schedules/releases against this federation's approved request(s) must show up here **live, with no page reload or re-login needed**.
- For each approved request, an expandable **tranche timeline** (horizontal or vertical stepper): each tranche's amount, planned date, and status badge — **Scheduled** (amber) or **Released** (green, with the actual release date once set).
- The state here is the *same underlying tranche records* Directorate edits in §2c — when Directorate clicks "Mark as Released" on a tranche, this view updates to reflect it immediately (same shared mock-data store, not a separate copy). Give the transition a small visible cue (e.g. the stepper step animates from amber to green, or a brief "Updated" pulse on the card) so it reads clearly in a live demo where you're likely to flip between the Directorate and Federation logins to show cause → effect.
- Running totals at the top of this section: Sanctioned (this request), Released-to-date, Remaining Scheduled — so a federation always knows what's still coming.

**3e. Our Achievers**
- Card grid of this federation's own achievers (reuse the Achiever card component from §2d/§4), with Tag/Edit/Remove — same two-step flow as Directorate's (pick from roster, then add Level/title/year/photo), but the athlete picker is **pre-filtered to this federation's sport** (same "default-filtered but removable" pattern already used for Squad creation), and Level is a free pick from the shared category list — federations can use existing levels but adding a brand-new custom category is Directorate-only (show the "＋ Add Category" option disabled/hidden here).
- A small note/preview: "This is how your achievers appear on the public portal →" linking to the public Achievers page filtered to this federation, so the federation can see their own public-facing showcase.

**3f. Our Public Profile (preview)**
- A read-only preview card of how this federation appears on the public **Associations** directory (§4c): name, sport, total funding granted (all-time), athlete/achiever count. Framed as "This is your public listing" — this is the pitch moment for why a federation would want to keep their data current.

---

## 4. Public portal additions (no login required, reachable from a state's public teaser panel / state view nav)

Add a small nav row (tabs or pills) visible on the state's public-facing pages: **Overview** (existing teaser) | **Grant Sanction Dashboard** | **Achievers** | **Associations**. Each of the three new pills renders only if its matching `StatePortalSettings` flag is on (§2e-ii) — if Directorate has turned one off, the pill doesn't appear in nav at all, and hitting its URL directly shows the "Not available for this state" state instead of the page.

**4a. Grant Sanction Dashboard** (`/state/:stateName/grants`)
- Public, aggregate-first transparency view. Stat cards: Total Budget Released, Total Disbursed, Federations Funded, Pending Requests (count only, no amounts on pending — sanctioned/disbursed figures only for anything not yet decided, to avoid publishing numbers that could still change).
- Below: a table of **approved** grants only — Federation, sport, sanctioned amount, disbursed-to-date, status. Clicking a row expands the tranche timeline (same stepper as §3d), so a citizen/journalist/federation can see exactly when money moved.

**4b. Achievers** (`/state/:stateName/achievers`)
- Any achiever in `featuredAchieverIds` (§2e-ii) renders first, in a highlighted spotlight row (same visual treatment as the National Games History "Top Performers" row), before the regular grid.
- Below that, the full public card grid, filterable by **Sport** and **Level** (chips/dropdown — includes any custom categories Directorate has added). Each card: achievement photo, athlete name, sport, district and federation (all pulled from the underlying `RegisteredAthlete` record, not stored on the tag itself), level badge, achievement text, year. Sort newest-first by default (excluding the featured row, which keeps Directorate's chosen order).

**4c. Associations** (`/state/:stateName/associations`)
- Public directory of the state's Federations, **excluding any federation with `listedPublicly: false`** (§2e-i). One card/row per remaining federation: name, sport, total funding granted (all-time, from approved grants), athlete/achiever count, "Active" status. This is the "resources" view referenced in the ask — keep it to funding + headline counts for this pass rather than inventing a facilities/infrastructure model that hasn't been scoped yet.

---

## 5. Mock data model

```
BudgetPool {
  id, stateId, cycleLabel,          // e.g. "FY 2026-27"
  totalAmount, status: "Open" | "Closed",
  releasedAt
}

GrantRequest {
  id, budgetPoolId, federationId,
  requestedAmount, utilizationPlan,
  sanctionedAmount?,                // set on decision
  status: "Pending" | "Approved" | "Partially Approved" | "Rejected",
  rejectionReason?,
  submittedAt, decidedAt?
}

DisbursementTranche {
  id, grantRequestId,
  amount, plannedDate,
  status: "Scheduled" | "Released",
  releasedAt?
}

Federation {
  id, stateId, name, sport,
  contactEmail,                      // for the dummy login mapping
  canSubmitGrantRequests: boolean,   // default true — Directorate toggle, §2e-i
  canTagAchievers: boolean,          // default true — Directorate toggle, §2e-i
  listedPublicly: boolean            // default true — Directorate toggle, §2e-i
}

StatePortalSettings {
  stateId,
  showGrantSanctionDashboard: boolean,  // default true — §2e-ii
  showAchievers: boolean,               // default true — §2e-ii
  showAssociations: boolean,            // default true — §2e-ii
  featuredAchieverIds: string[]         // ordered, max 6 — §2e-ii
}

AchievementLevel {
  id, label,                         // "National" | "International" | "State" | custom
  isCustom: boolean
}

Achiever {
  id,
  athleteContactId,                  // reference to RegisteredAthlete.contactId — the tag, not a copy.
                                      // name/sport/district/gender all resolved via this lookup, never duplicated here
  levelId, achievementTitle, year,
  photoUrl?,                         // achievement-specific photo, separate from any core athlete record
  taggedByFederationId,              // which federation applied the tag (Directorate tags carry the athlete's own federation)
  addedBy: "Directorate" | "Federation"
}
```

**Resolution rule:** anywhere an Achiever is rendered (dashboard cards, public grid), look up `athleteContactId` against the existing `RegisteredAthlete` list to get name/sport/district/gender — don't store or edit those fields on the Achiever tag itself. One `RegisteredAthlete` can have multiple `Achiever` tags over time (different years/levels).

Seed data: one open `BudgetPool` for Meghalaya (₹2,00,00,000, "FY 2026-27"). 3–4 `Federation` records (Archery, Football, Boxing, Wrestling — reuse the sports already used in National Games History). For Archery specifically (the demo login), seed one **Approved** `GrantRequest` with 2 tranches (one Released, one Scheduled) and one **Pending** request state to demo both — actually: since only one open request is allowed at a time in this prototype, seed Archery with one **Approved, fully-scheduled** request so the tranche timeline has something to show, and leave the "+ Request Grant" button live for a fresh demo request. Seed the other 2–3 federations with a mix of Pending/Approved/Rejected so the Directorate's Grant Requests table isn't empty or monotonous. Seed 8–10 `Achiever` records, each `athleteContactId` pointing at a real existing `RegisteredAthlete` from the already-generated roster (don't invent new names for these) — spread across the 3 default levels plus one custom level (e.g. "Zonal") so the "＋ Add Category" story is visible without needing to click it live, and across a few different sports/federations so the public grid's filters have something to actually filter.

---

## 6. Visual direction

Same system as the rest of the prototype: one accent color, card-based composition, soft rounded corners, stat cards large-bold-number/small-muted-label, badge colors consistent across screens (e.g. green = Approved/Released, amber = Pending/Scheduled, red = Rejected). Tranche timelines/steppers should use the same micro-interaction language as the existing filter/map hover states — this needs to demo smoothly, not just look correct in a screenshot.

---

Build all of this with working mock-data interactivity: role-based login actually routes to the right dashboard, submitting a Grant Request actually appends to the list and updates stat cards, approving/scheduling tranches actually updates the Federation's tracker and the public Grant Sanction Dashboard live, adding an Achiever actually appears in both the dashboard grid and the public Achievers page, and flipping any toggle in Permissions & Display (§2e) actually changes what that Federation login can do and what the public portal shows — no reload needed. This is the walkthrough for both a Directorate and a Federation stakeholder, so both logins need to feel like a real, current system, not a static mock.

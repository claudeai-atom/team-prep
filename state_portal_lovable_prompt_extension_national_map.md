# Extending the map: National Distribution Map (Master Admin + Stakeholder Portal)

This introduces one reusable component — a permission-aware India choropleth — used in two places: inside the Master Admin panel (unrestricted), and as the landing screen of a brand-new **Stakeholder Portal** for Federation/Director logins (which didn't have any consuming UI yet — this is that missing piece).

Two design calls worth flagging before the prompt:
- **States outside a stakeholder's grant look identical to "not yet onboarded"** — muted, no hover data, no drill-down. A Federation shouldn't be able to tell "no data exists here" apart from "data exists but I don't have access," which is the safer default.
- This map surfaces **R1 (Team Prep) counts only** — aggregate numbers, never named athletes, so it works the same regardless of a grant's Aggregate/Named detail level. Named-athlete drill-down is separate future scope.

---

# Lovable Extension Prompt — National Distribution Map (Master Admin + New Stakeholder Portal)

Paste this into the **same Lovable project**. Don't touch the existing Global View, `/login`, State View tabs, Team & Trials, or the Master Admin panel's States/Stakeholder Access tabs — this is additive: one new shared map component, a third Master Admin tab, and an entirely new Stakeholder Portal area with its own login. Match the existing visual style (accent color, card style, type scale).

---

## 1. Shared component: National Distribution Map

A full India choropleth map, reusable with a `scope` prop (`{ sports: "All" | string[], states: "All" | string[] }`) that the two usages below pass in differently.

**Color coding:** sequential fill by athlete count within scope — light tint for low counts, deep accent for high counts (a simple 4–5 step scale is enough). States with **zero data in scope** (not yet onboarded, or outside the viewer's grant — visually identical, no distinction shown) render fully muted/grey with a disabled cursor.

**Hover (in-scope, non-zero states only):** tooltip with state name, Total Athletes (within scope), Districts Covered. No tooltip / no highlight on muted states beyond a plain state-name label.

**Click (in-scope, non-zero states only):** opens that state's **District Drill-Down** — reuse the existing Team Preparation district-map + ranked-district-list + sport-breakdown pattern, but strictly **read-only** (no upload, no edit affordances, small "Read-only" badge) and pre-scoped: if `scope.sports` is a single sport, the breakdown shows only that sport (no sport switcher); if `scope.sports` is "All", show the full multi-sport breakdown exactly as the state's own dashboard does. Clicking muted states does nothing (cursor shows not-allowed).

---

## 2. Usage A — Master Admin "National Map" tab

Add a third tab to the existing Master Admin panel (`/admin`), next to States and Stakeholder Access: **National Map**.

- Renders the map with `scope = { sports: [selectedSport] or "All", states: "All" }` (Master Admin is never grant-restricted).
- A **Sport filter dropdown** above the map, defaulting to "All Sports," listing every sport across all active states plus "All Sports." Selecting a sport lets the admin preview exactly what a Federation for that sport would see — useful for QA before handing out stakeholder credentials.
- District drill-down works the same as described above, respecting the current sport filter.

---

## 3. Usage B — New Stakeholder Portal (`/stakeholder/login` + `/stakeholder`)

A third, separate login area — distinct from `/login` (states) and `/admin/login` (Khelo Tech).

**`/stakeholder/login`:** same visual pattern as the other login screens, with a dashed-border demo credentials box showing **both** seeded stakeholder logins so either can be demoed:
- `archery.federation@atom.demo` / `Archery@2027` → Archery Federation of India
- `director.sai@atom.demo` / `Director@2027` → Director, Sports Authority of India

On success, route to `/stakeholder` and derive that stakeholder's map `scope` from their `Stakeholder.grants` record already defined in the Master Admin prompt (resource R1's `stateScope`/`states`/`sportScope`/`sports`, minus any `exceptions`).

**`/stakeholder` landing:** header shows "Logged in as [Stakeholder Name]" plus their sport tag if Federation, and a Log Out action. Below it, the National Distribution Map full-bleed, scoped to their grant — no sport filter control for them (their scope is fixed by the grant, not user-selectable). District drill-down behaves exactly as in section 1.

---

## 4. Mock data

No new athlete data needed — **derive every state-by-sport total shown on the map from the existing `RegisteredAthlete` records already seeded per state**, don't hand-author a separate aggregate table, so the map's numbers always reconcile with each state's own dashboard.

Add `credentials` to the two Stakeholder records already seeded in the Master Admin prompt:
```
// Archery Federation of India
credentials: { username: "archery.federation@atom.demo", password: "Archery@2027" }

// Director, Sports Authority of India
credentials: { username: "director.sai@atom.demo", password: "Director@2027" }
```

---

Do not change the existing Global View, `/login`, State View tabs, Team & Trials, or the States/Stakeholder Access tabs in Master Admin — this adds one reusable map component, a third Master Admin tab, and the new `/stakeholder/login` + `/stakeholder` portal only.

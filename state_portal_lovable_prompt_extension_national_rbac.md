# Nomenclature & assumptions — corrected 2026-09-23 against the live app

This doc originally assumed no Master Admin console existed and specced a new one from scratch. **That was wrong** — checked the live app at `khelo-team-preparation.lovable.app/admin` (login `admin@khelotech.com` / `Admin@2027`, per `state_portal_lovable_prompt_extension_national_map.md` which is already live) and it already has three tabs: **States**, **Stakeholder Access**, **National Map**. Confirmed: 5 active states, 19-state master list, Activate/View Credentials/Reset Password/View Dashboard all working on States; Stakeholder Access shows "2 stakeholders" and "Resources governed: 3 — Roster, History, Long List" (= R1/R2/R3 from the Team Preparation stakeholder-access model).

**This means the existing `Stakeholder` entity already IS what the business conversation called "National Director" and "National Federation."** Archery Federation of India (Federation-type, already seeded) and Director, Sports Authority of India (Director-type, already seeded) are live examples. IOA and AIFF are just two more `Stakeholder` records, not a new schema — use "National Director" / "National Federation" only as **display labels** in the UI (to read clearly against the new "State Federation"/"Directorate"), never as separate underlying entities.

**What's genuinely missing, revised:**
1. The Stakeholder grant grid caps at 3 resources. This pass adds **R4 — Grants/Disbursement** and **R5 — Achievers**.
2. No stakeholder can create another stakeholder today — only Master Admin can. IOA creating AIFF needs a new **cascading creation right**, Director-type only.
3. **State Admin creating Directorate + State Federation** is untouched by any of the above — it's within one state, not cross-tenant, so the Stakeholder model was never going to cover it. Still net-new, unchanged from the original version of this doc.

---

# Lovable Extension Prompt — Extend Stakeholder Access, Add State-Level Provisioning

Paste into the **same Lovable project**. Do **not** rebuild the Master Admin panel, its States tab, its National Map tab, or the Stakeholder Portal's existing map landing — all of that already exists and works. This is additive: two new resources on the existing grant grid, one new creation right, and a new tab inside the existing State Admin dashboard.

---

## 1. Extend the existing Stakeholder Access resource grid

Wherever the resource/scope/detail grant grid is built today (Master Admin's Stakeholder Access tab, and anywhere a stakeholder's own grant is summarized), add two more resources alongside the existing three: **R4 — Grants/Disbursement**, **R5 — Achievers**. Same scope dimensions as R1–R3 (state scope, sport scope, detail level: Aggregate vs Named), same subtractive-exception mechanic already in place. Update the "Resources governed" summary metric from 3 to 5.

## 2. Cascading creation — a Director-type stakeholder can create a Federation-type one

On the Stakeholder Portal (`/stakeholder`), for a **Director**-type login only (hidden entirely for Federation-type logins), add a **"+ Create Federation Stakeholder"** action. It opens the same create-stakeholder → build-grant flow Master Admin already uses, pre-locked to Federation type with a required sport picker. The new stakeholder appears immediately in Master Admin's Stakeholder Access list, tagged **"Created by: [Director name]"** for provenance, and is immediately usable as a login — no reload needed.

## 3. Stakeholder Portal — surface Grants and Achievers per the stakeholder's own grant

`/stakeholder` currently only shows the National Map. Add two more sections below it, each rendered only if that resource is in the logged-in stakeholder's grant: **Grants** (reuse the public Grant Sanction Dashboard's table + tranche-timeline pattern, aggregated across their state scope, respecting Aggregate vs Named detail) and **Achievers** (reuse the public Achievers grid, same scoping). Sport-locked for a Federation-type stakeholder, sport-filterable for a Director-type one.

## 4. State Admin — new "Manage Access" tab (unaffected by §1–3, still net-new)

Add a fourth tab to the existing State Admin dashboard (alongside Team Preparation, National Games History, Team & Trials): **Manage Access**.

- Two lists: **Directorate** (one per state — show the existing one instead of a create button once it exists) and **State Federations**.
- "+ Create Directorate": simple form (state is implicit), generates a demo credential pair, e.g. `directorate.{state}@atom.demo`.
- "+ Create State Federation": name + sport dropdown, generates credentials, e.g. `{sport}.{state}@atom.demo`. New federations must immediately appear in the Directorate's Federation Permissions table (grants/achievers doc §2e-i) and in the Federation login role-selector.

## 5. Seed data

Extend the 2 existing seeded stakeholders with 2 more, both granted all 5 resources at Named detail so the extended grid has something to demo:
- **Indian Olympic Association** — Director type (same preset as the existing SAI Director: all-sport, all-state). Credentials `director.ioa@atom.demo` / `Ioa@2027`.
- **AIFF** — Federation type, sport = Football, all-state. Credentials `football.federation@atom.demo` / `Football@2027`. Marked **"Created by: Indian Olympic Association"** to demo §2's provenance live.

Give AIFF real Football athletes/achievers already present across 2–3 seeded states (reuse existing Football-tagged athletes, don't invent a new roster) so its map/Grants/Achievers sections show real data on first login.

---

Build all of this with working mock-data interactivity: creating a stakeholder (via Master Admin or a Director's cascading creation) or a Directorate/State Federation actually adds it to the relevant list and makes it usable as a login immediately, and the extended R4/R5 grant fields actually gate what a stakeholder sees on their portal — same shared data layer as the rest of this prototype, not a separate mock.

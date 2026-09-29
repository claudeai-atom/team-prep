# Team Preparation Tool — Blueprint & End-to-End Flow

_Written 2026-09-18. Vision capture + open-questions pass, not yet reviewed or built._

## Where this sits

A free-tier, state-scoped tool that sits in front of GMS on the Khelo Tech website. Purpose is dual: (1) give Directors of Sports / SAI centers / Federations better visibility and a trials pipeline ahead of any GMS-hosted event, and (2) act as ATOM's lead-gen wedge — states get real value for free, which is the on-ramp into paid ATOM ecosystem modules.

Builds on the existing State Athlete Data Portal prototype (`state_portal_lovable_prompt*.md` in this folder): Global View map, per-state login, Team Preparation tab, National Games History tab, Athlete Listing table. This doc is the product blueprint behind that prototype's next phase, not a replacement for it.

## Actors

- **Khelo Tech Master Admin** — platform owner, activates states.
- **State Admin** — one credential set per state, uploads data, runs Team & Trials.
- **GMS** — the events system (e.g. 39th National Games) this tool feeds into.
- **Federation / Director of Sports / SAI Center** — downstream data consumers, sport- or scope-specific (access model TBD — see open questions).
- **Athlete** — data subject, not a platform login.

## End-to-end flow

**Stage 0 — Activation (Master Admin, one-time/state)**
Master Admin activates a state, the state's districts are pre-loaded from a canonical master list, and Master Admin issues that state's login credentials. State now shows as "active" on the public map.

**Stage 1 — Blank-state onboarding (State Admin, first login)**
State logs in and sees their district list with everything zeroed — no athletes, no charts populated — plus a prominent "Upload Athlete Data" CTA.

**Stage 2 — Bulk upload (State Admin)**
State downloads Khelo Tech's predefined CSV/Excel template, fills it, uploads it. System validates rows against the predefined district list and ingests them.

**Stage 3 — View-only dashboard (Team Preparation tab)**
Dashboard populates from the upload: overview stats, district map, ranked district list, sport breakdown, gender/age splits, athlete listing table. View-only — no inline editing, corrections come via re-upload.

**Stage 4 — Enrichment fields (new)**
Athlete records carry two more attributes: **Level of Player** (District/State/National/International) and **Training Location** (SAI center/academy/etc.), so Federations and Directors can filter/report by these.

**Stage 5 — Team & Trials (new)**
State selects athletes off their own roster and groups them into a self-made bunch/shortlist for a specific event or discipline — a trials squad, not a registration.

**Stage 6 — Handoff to GMS (two models, pick one for MVP)**
- **A — Export:** bunch exports as a file, state manually uploads it into GMS's own registration flow.
- **B — Integrated push:** GMS events are listed inside the tool, state pushes the bunch straight in as a **Long List** attached to that event — no file round-trip.

**Stage 7 — Discipline nomination under quota (GMS-owned, downstream)**
From the Long List, someone nominates athletes into specific disciplines subject to quota limits per state. This is explicitly "later" and outside this tool's scope — it only needs to hand off a clean Long List. Note: this is the same mechanism already flagged as future scope in the TMS Sports Library PRD (Participant Nature/Officiating → Quota Allocation) — worth keeping in sync when TMS actually builds it, not reinventing it here.

**Stage 8 — Downstream consumption**
Federations/Directors/SAI centers use the enriched data for their own planning. How they actually access it isn't defined yet (see open questions).

## External stakeholder access model (added 2026-09-18)

Resolves open question #7. Federations and Directors are cross-tenant read-only viewers — Federations are sport-scoped across all states, Directors are all-state/all-sport — so this can't be a per-state login like State Admin. It needs to be a permission grid, not fixed roles, consistent with how RBAC is already designed elsewhere in the product (Ticketing: permissions are primitives, roles are just presets; BMS: scope is granted, never inherited from data ownership). Same philosophy here, just applied across tenants (states) instead of within one org.

**Resources to gate (three, not four):**
- **R1 — Team Preparation data**: current-cycle per-state roster + enrichment fields (Level of Player, Training Location).
- **R2 — National Games History**: past-edition data per state (medal tally, top performers, athletes by sport).
- **R3 — Long List**: event-scoped shortlists states push toward a GMS event, pre-nomination. This is the one that only exists here — GMS never has it, since GMS only ever holds the final nominated subset. This is a standalone value point for stakeholder access even with zero GMS integration.
- **Explicitly not a resource**: Nominated/final entries — that's GMS's system of record, not duplicated here (at most a status pointer later).

**Three independent scope dimensions per grant:**
- **State scope**: All states / a named subset / one state
- **Sport scope**: All sports / a named subset / one sport
- **Detail level**: Aggregate-only (counts/stats, no names) vs. named-athlete detail (full roster rows)

A grant is set **per resource** — a stakeholder's R1 access can differ from their R3 access (e.g., full detail on Long List for their sport, aggregate-only on History).

**Grant = allow, with subtractive exceptions:** base grant is additive (e.g. "all states"), with an optional deny-override list layered on top — e.g. Khelo Tech excludes one specific state from an otherwise all-states federation grant. Mirrors the same "scope granted, never earned" + subtractive-exception pattern already used in BMS RBAC and Ticketing's own-scope delegation: `grant(resource, state_scope, sport_scope, detail_level) + exceptions: [state]`.

**Federation / Director are presets, not hardcoded roles:**
- **Federation preset**: Sport = single (theirs), State = all, Detail = named, Resources = R1+R2+R3.
- **Director preset**: Sport = all, State = all, Detail = named, Resources = R1+R2+R3.

These just pre-fill the grant builder — the mechanism underneath is the same grid for both, so a future variant (e.g. a state-level Director, or a Federation with aggregate-only access) needs no new code, just a different grant.

**Master Admin workflow (ordering matters):** credentials can't be the first thing created. Sequence is: create stakeholder record (name, type) → build grant(s) across the resource/scope/detail grid → *then* issue login. Access configuration is a precondition for the account existing, not something bolted on after.

## Use cases

| # | Actor | Use case |
|---|---|---|
| UC1 | Master Admin | Activate a state, pre-load its districts, issue credentials |
| UC2 | State Admin | First login → blank-state dashboard |
| UC3 | State Admin | Bulk-upload athlete data via template |
| UC4 | State Admin | View/filter Team Preparation dashboard (district/sport/gender/age) |
| UC5 | State Admin | Enrich athletes with Level of Player + Training Location |
| UC6 | State Admin | Create a Team & Trials bunch from the roster |
| UC7 | State Admin | Export bunch, or push it as a Long List to a listed GMS event |
| UC8 | GMS/TMS actor | Nominate from Long List under quota (out of scope, handoff only) |
| UC9 | Federation/Director/SAI | Consume state data for their sport/scope, per their access grant |
| UC10 | State Admin | Re-upload/update data (semantics TBD) |
| UC11 | Master Admin | Create a stakeholder record and build their access grant(s) (resource × state-scope × sport-scope × detail-level, plus exceptions) before issuing credentials |
| UC12 | Federation | View named-athlete Long List + roster + history for their sport, across all (or an exception-limited set of) states |
| UC13 | Director | View named-athlete data across all states and all sports |

## Open questions before this can be built

1. **District master data** — who owns/maintains the predefined per-state district list? Same governance question this product already has for BMS/VMS geo data.
2. **Re-upload semantics** — does a new upload replace, append, or upsert/dedupe (presumably by phone/email as the unique athlete ID, per the existing prototype's model)?
3. **Template versioning** — can Level/Training Location be added as new columns without breaking states that already uploaded under the old template?
4. **Team & Trials scope** — one bunch per event/discipline or unlimited ad hoc bunches? Can an athlete sit in more than one bunch at once?
5. **Export vs. integrated push (Model A vs B)** — build export first and integrate later, or go straight to B?
6. **Nomination actor** — state, federation, or a GMS/TMS admin performs the quota-constrained nomination off the Long List? Determines whether this tool needs any UI there at all.
7. ~~Federation/Director/SAI access~~ — resolved by the access-grant model above (grid of resource × state-scope × sport-scope × detail-level, with subtractive exceptions).
8. **Free-tier boundary** — is Team & Trials itself still free, or is it the upsell trigger, with only "editing/operational tooling" reserved for paid?
9. **Post-conversion data** — when a state upgrades to a paying ATOM client, does this uploaded data seed their AMS/BMS instance, or stay siloed?
10. **State consent** — does a state need to know/agree that a Federation or Director can see their uploaded data? Right now states treat the portal as "their own"; cross-tenant visibility is a data-sharing policy decision, probably belongs in the free-tier terms states accept at Stage 0/2, not silent.
11. **Detail-level granularity** — is detail strictly binary (aggregate vs named), or does Long List need a middle tier (names visible, contact info masked)?
12. **Sub-state scope** — you specified State + Sport as the grant axes; is there ever a need to scope a stakeholder below state level (e.g. a district sports officer)?
13. **Grant lifecycle** — when a state's data changes (re-upload, new district added), do existing grants auto-apply to the new data live, or does Master Admin need to re-touch each grant? (Should be live evaluation, not a snapshot.)

## Next steps
- Resolve open questions above (with product/business team where they're not purely technical calls).
- Run this blueprint past the sportstech-reviewer agent to pressure-test cross-module conflicts (GMS/TMS quota linkage, RBAC/access model for Federation-scoped roles) before extending the Lovable prototype.

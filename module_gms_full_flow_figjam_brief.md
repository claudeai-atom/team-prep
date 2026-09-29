# Team Prep / GMS ecosystem — full flow brief (for FigJam + pressure-test)

_Written 2026-09-24, consolidating the Team Preparation E2E blueprint, the Grants/Achievers + National RBAC Lovable extensions, and new scope from the 2026-09-24 conversation. Source-of-truth companions: `module_gms_team_preparation_e2e_flow.md`, `state_portal_lovable_prompt_extension_grants_achievers.md`, `state_portal_lovable_prompt_extension_national_rbac.md`._

## Actors

- **Master Admin** (Khelo Tech) — activates States; creates/manages Stakeholder accounts (National Federation/National Director, display labels over the existing `Stakeholder` entity); manages the platform-wide permission grid.
- **State Admin** — per state, created by Master Admin. Uploads athlete data, runs Team & Trials/Long List, creates Directorate + State Federation accounts.
- **Directorate** — per state, created by State Admin. Releases Budget Pool, reviews/approves/schedules Grant disbursement, manages Federation permissions + public display.
- **State Federation** — per state per sport, created by State Admin. Requests grants, uploads athletes (own sport), tags achievers, manages own Resources.
- **National Federation / National Director** — cross-state Stakeholder accounts (Federation/Director presets), read-only, granted access across R1–R5.
- **Athlete** — data subject, not a login. Unique by **email (primary key)**, **phone (secondary key)**.
- **Resource** _(new, undefined — see gaps)_ — Support staff, Physio, etc., attachable to a State or a State Federation.
- **GMS** — downstream events system; receives the Long List, owns Discipline nomination under quota.

## Flow, in order

1. **Activation** — Master Admin activates a State → its ecosystem (districts, empty dashboard) comes online.
2. **Upload** — State Admin bulk-uploads athletes via predefined CSV template → dashboard populates: district, age, gender, sport (+ whatever other breakdowns exist).
3. **Provisioning** — State Admin creates Directorate + State Federations (e.g. "Football Federation of Meghalaya"), each with permissions scoped by federation type.
4. **Roster growth** — Directorate/State Federations can also upload athletes into the same central roster, deduped by email/phone.
5. **Grant flow** — Directorate (or State — see gap #4) releases a Budget Pool → visible on Federation's dashboard → Federation applies with a reason + supporting document → Directorate reviews and approves out of the budget, disbursed in **tranches** with date/time tracking → visible to the Federation and publicly.
6. **Achievers** — State and State Federations tag existing (unique) athletes with achievement levels — never new athlete records.
7. **Team & Trials → GMS handoff** — State builds a squad/Long List, exports it and/or sends it directly to a selected GMS event.
8. **Resources** — Support staff/Physio etc. can be added to a Federation or a State; permission-managed through whichever state they belong to.
9. **National tier** — Master Admin manages permissions for State/National Federation/National Director levels, who get nation-wide data access via a visual dashboard, gated by the resource/scope/detail grant grid (R1 Roster, R2 History, R3 Long List, R4 Grants, R5 Achievers).
10. **Permission-gated data visualization** — the flow itself needs to visibly show what data moves where, conditioned on whether a permission grant allows it.
11. **Pre-nomination enrichment** — once data reaches GMS as a Long List, additional data must be captured per athlete before they can be nominated into an Event Discipline (fields undefined — see gap #2).

## Open gaps / questions (flagging before building, per request)

1. **Resources (Support staff/Physio) is an undefined entity.** No fields, no defined capabilities (directory listing only, or a login with its own view?), and no stated relationship to AMS, which already owns staff/training-adjacent scope. Real risk of silently duplicating or conflicting with AMS territory — this is exactly the kind of question `module_boundaries_bms_ticketing_ams_tms` exists to answer, and this module isn't in that boundary map at all yet.
2. **"Additional data before nominating into an Event Discipline" is undefined**, and it sounds like the same thing already flagged as future TMS/GMS scope: Participant Nature/Officiating → Quota Allocation in the TMS Sports Library PRD. Risk of inventing a second, conflicting version of that data requirement here instead of pointing at the one already flagged.
3. **Athlete uniqueness (email primary / phone secondary) has no stated merge rule.** Now that State, Directorate, and State Federation can all upload into the same central roster, what happens on a collision — State uploads an athlete, Federation later uploads the same person with a different phone number? This was already an open question in the original E2E doc (#2, re-upload semantics) and is more urgent now that it's multi-writer, not single-writer.
4. **"State or Directorate releases a budget"** — the corrected actor hierarchy has Directorate as the sole budget authority, with State Admin only provisioning accounts, not operating grants. Need to confirm this phrasing is just colloquial shorthand for Directorate, not an actual second release path (which would reopen whether there's one shared budget pool or two).
5. **Grant application "supporting document"** isn't in the current data model (only a text utilization plan exists) — needs a document-upload field and a decision on whether Directorate must open/review it before approving.
6. **No visual grammar decided yet for permission-gated data flow on the FigJam itself** — worth fixing before drawing (e.g. swim-lane per actor + a parallel lane for gated resources, arrows color-coded by R1–R5, sticky callouts on gated edges) — matches the "parallel sticky lane for validations" convention already used on the TMS Create→Schedule board, worth reusing rather than inventing a new visual language.
7. **Relationship to existing FigJam boards** — is this a standalone new board (the pasted "Team Prep" board), or should it adopt conventions from the TMS Flow board's GMS/Axis dataflow FigJam (tinted zones, reference tables)? Affects how it should be organized before building.

## Decisions — round 2 (2026-09-24, after sportstech-reviewer pressure-test)

Full pressure-test findings held by the session (not re-pasted here in full); these are the decisions made in response, resolving the 7 self-identified gaps:

1. **Resources — defined.** Type = Support Staff, with subtypes: Coach, Physiotherapist, Biomechanist, Doctor, Physiologist, Psychologist, Strength & Conditioning Trainer, Young Professional. Attached to a Federation or a State. Bulk-uploadable like athletes, **plus** a manual "Add User" flow (define user type → name/details → add). Zero or one sport (optional, single). Treated as a **directory record, not a login/principal**, for this pass — no independent dashboard or capability defined yet. Still open: if a Resource ever needs to log data (e.g. a physio logging a session), that's an identity question that should route through however the platform resolves the tenancy question below, not be invented fresh here. Note this reopens a decision `state_portal_lovable_prompt_extension_grants_achievers.md` §4c had explicitly closed the other way ("keep Associations to funding + headline counts... rather than inventing a facilities/infrastructure model") — worth being aware it's being reopened, not silently overwritten.
2. **Pre-nomination enrichment (Aadhar ID etc.) — explicitly out of scope here.** This tool hands off the Long List; the specific field set is GMS's to define (per ATOM's Dynamic Forms being per-Project, and Sports Library ED eligibility rules) — the FigJam should draw this as a GMS-side step reading a Project-owned form, not a Team Prep field list.
3. **Athlete uniqueness — composite (State, Sport, UserID), UserID system-assigned via matching (see chat), reject-on-duplicate.** Requires a new Edit-Athlete action to preserve a correction path, since reject-on-duplicate breaks the old "corrections via re-upload" assumption from E2E Stage 3 — that assumption is now superseded.
4. **Grant release — dual path, by delegation, not automatic.** State releases by default; State can grant Directorate release authority; when delegated, Directorate's release activity syncs back to State for monitoring (State keeps read visibility either way). Not yet decided: whether release/approve should still be split across two *different* principals (maker-checker) even within this delegation model — flagged as a still-open strengthening, not blocking.
5. **Grant supporting document — real attachment, not text.** `GrantRequest` gets a document/file field, not just the utilization-plan text. Still open: who can read the attachment (Directorate only, or does it inherit the public Grant Sanction Dashboard's visibility) — needs an explicit access-tier decision before that page goes live, since a utilization-plan attachment can carry sensitive figures.
6. **Visual grammar for the FigJam — decided (below), to lock before drawing.**

## Visual grammar for the FigJam (decide-before-drawing, per gap 6)

Reuses the TMS Create→Schedule board's parallel-sticky-lane convention rather than inventing a new one:

- **Primary lanes = actors**, in flow order: Master Admin → State Admin → Directorate → State Federation → (shared) Athlete/Roster → GMS, with National Stakeholder as a parallel side-lane (it's read-only and cross-cutting, not sequential with the others).
- **Ownership tag on every node** — a small colored strip/badge per entity marking it ATOM-owned / Team-Prep-owned / GMS-owned / TMS-owned, so cross-module ownership is visible at a glance instead of implied.
- **Trust-boundary marker** — a distinct thick dashed line drawn at the exact point unverified, CSV-sourced athlete data crosses into the GMS Long List handoff. This is the single highest-value visual element per the pressure-test — make it impossible to miss.
- **Gated vs ungated edges** — arrows carrying data to a National Stakeholder (permission-gated, R1–R5) are dashed and labeled with the resource code plus a small gate/padlock icon; internal/ungated flows are solid.
- **Open-question stickies** — orange stickies (matching existing board convention) on any node still carrying an unresolved risk from the pressure-test (consent, tenancy, money maker-checker, audit trail) so the board documents known risk rather than hiding it.

## Still open, not yet resolved (carried forward from the pressure-test, unaddressed by round-2 decisions above)

- **Tenancy**: is this stack (State/Directorate/State Federation/Stakeholder) inside ATOM's Institution/Project model, or a deliberately separate system with its own migration contract on conversion? Foundational — changes the diagram's actor lanes if answered "inside."
- **Consent for minors** on the public Achievers page — no flow decided; flagged as the largest legal-exposure item in the pressure-test (DPDP, not just a product preference).
- **Money controls** — maker-checker (initiate ≠ approve) and person-level audit trail (Created By, append-only log) are undecided; current model is one shared Directorate credential doing everything with a self-asserted "Released" status.
- **Permission model duplication** — the Stakeholder resource-grid (R1–R5) and Directorate's per-Federation boolean toggles are two different mechanisms describing similar things; not yet unified.

## Next step
Visual grammar is locked above. Remaining open items (tenancy, consent, money controls, permission-model duplication) can either be resolved now or carried onto the board as explicit open-question stickies and revisited — need a call on which before building starts.

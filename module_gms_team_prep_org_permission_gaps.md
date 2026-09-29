# Team Preparation — Org/Permission Gap Analysis (from 2026-09-26 meeting)

_Written 2026-09-27. Gap analysis only, from raw meeting notes — no solutioning yet. Sits on top of `module_gms_team_preparation_e2e_flow.md`, `state_portal_lovable_prompt_extension_grants_achievers.md`, and `state_portal_lovable_prompt_extension_national_rbac.md`._

## Source

Meeting notes (2026-09-26) pasted in full below the analysis, covering: product direction (configurable org/sport/dataset/workflow permissions, not name/type-based), Sachin's use cases, Pratish's use cases, Richa's use cases (input pending — not captured), flows needing validation, 6 inconsistencies/gaps, 7 open questions.

## Headline verdict

The current build has three **fixed** org types — State Admin, Directorate, State Federation — plus a cross-tenant Stakeholder grid (National Director/Federation) that's separately mechanized. The meeting asks for **configurable** orgs (name/type shouldn't determine capability). Those two are not compatible yet, and most gaps below trace back to that one mismatch.

## Sachin's use cases — gap table

| # | Use case | Status | Gap |
|---|---|---|---|
| S1 | Separate team prep from funding | Partial | Team & Trials is State Admin-only; a Federation can upload a roster but can't build squads/camps/push Long Lists. Directorate owns grants + achievers + public toggles, so funding and recognition/publishing are bundled in one role. |
| S2 | Grant accountability — expenditure + utilisation certificate | Missing | "Utilization plan" is pre-spend text only. Tranches only have Scheduled/Released. No expenditure record, certificate, document upload, or funder review of spend. |
| S3 | National ≠ state bodies, no single hierarchy | Partial/Conflicts | National Federation is read-only (Stakeholder grid). State Federation is a separate entity. Nothing links e.g. AIFF to a state federation, so "manages/receives reporting from" has no home — and read-only contradicts "manage." |
| S4 | Directorate not required in every state's flow | Conflicts | A grant only exists if the Directorate releases a Budget Pool; Directorate is also the sole achievers/public-toggle owner, one per state. No route where a different body (sports authority, department) funds instead. |
| S5 | "State" = data scope, not the account | Partial | Stakeholder grid does separate state scope from stakeholder identity for cross-tenant readers. In-state, the State Admin login IS effectively the state's account (Master Admin issues "that state's" credentials) — scope and account-holder still fused there. |

## Pratish's use cases — gap table

| # | Use case | Status | Gap |
|---|---|---|---|
| P1 | Grants sport-wise, not just district-wise | Partial | Requests are per-Federation (sport-scoped by proxy), but Budget Pool is state-wide, not split by sport. One open request at a time; no purpose field (camp/event/equipment). |
| P2 | Requested / sanctioned / disbursed distinct | Mostly covered | All three amounts + tranches exist. Missing: pool-level balance check against sanctions; payment references; tranche gating (e.g. no next tranche before prior utilisation reported). |
| P3 | Utilisation tracked against payments | Missing | Same root as S2 — nothing links spend to a specific tranche. |
| P4 | Preparation beyond National Games | Partial | Long List handoff targets GMS events only; quota nomination is GMS-owned; non-GMS/international events have no path. History tab is National-Games-specific. |
| P5 | Resolve org hierarchy before building | Conflicts | Built hierarchy is fixed: Master Admin → State Admin → Directorate/State Federation, and Master Admin/National Director → National Federation. No state Olympic association, sports authority, or department modeled. |

## Flows needing validation — findings

- **Onboarding:** only cross-tenant Stakeholders get a configurable grant before credentials issue. State Admin/Directorate/State Federation start with fixed, hardcoded capabilities.
- **Data management:** sport restriction on Federation's roster view is a removable UX filter, not access control. Re-upload semantics (replace/append/upsert) still open — a State re-upload could wipe Federation-added rows. Ownership/dedupe between State and Federation uploads undefined.
- **Data visibility:** R1–R5 grid gates cross-tenant readers only; no field-level publish control in-state. Public achiever cards publish name/photo/district with no consent step or approval gate.
- **Achievers:** a Federation's tag goes public immediately — no review/verification step.
- **Event prep:** Long List handoff is defined; quota-check boundary and event-specific fields are not.
- **Grants:** requests are text-only, no supporting documents; one actor (Directorate) does review+sanction+release, so rights can't be split across bodies.
- **Public page:** controls are 3 tab toggles + Featured Achievers list — nothing per-section or per-figure, and no "approved data" gate before publish.

## The 6 inconsistencies (meeting notes §4) — confirmed against the build

1. **State doing two jobs** — partly resolved cross-tenant, unresolved in-state (see S5).
2. **Fixed hierarchy won't cover the examples** — confirmed; this is the root gap.
3. **Grant demo doesn't represent financial status fully** — request/sanction/tranche exist, utilisation+balance don't.
4. **District views don't cover sport-led requests** — confirmed, filter is removable/UX-only.
5. **Permissions need to govern actions, not just screens** — confirmed: Directorate has 3 toggles, the R1-R5 grid is read-only, State Admin is hardcoded.
6. **Event prep must be reusable** — confirmed, currently GMS-bound.

## Root gaps to solve (in dependency order)

1. **No org-agnostic permission model.** Three separate mechanisms today: cross-tenant R1–R5 grid, Directorate's 3 toggles, hardcoded State Admin capabilities. None has an action dimension (view/edit/tag/request/approve/release/publish independently grantable, per meeting §4.5).
2. **No org-relationship model.** Who reports to / approves for / funds whom isn't stored anywhere, so it can't vary by state or sport (blocks S3, S4, P5).
3. **No utilisation & evidence layer.** Expenditure records, certificates, document upload, funder review of spend (blocks S2, P3).
4. **No publish-approval layer.** Nothing controls what goes public, who approves it, or athlete consent (blocks data-visibility flow, Q10/Q11 below).
5. **Event-agnostic preparation.** Rosters/Long Lists are tied to GMS events; needs to generalize (blocks P4, meeting §4.6).

## Open questions — status

From the meeting (§5), cross-referenced against the E2E doc's own open questions:

- Which org gets the primary account per state/client, and who can grant access to others? → unresolved, blocks root gap #1/#2.
- Who approves/sanctions/releases a grant and receives the UC — can these split across bodies? → unresolved, currently one actor (Directorate).
- What reporting relationships (national fed ↔ state assoc ↔ state dept ↔ Olympic assoc) are informational vs. approval-conferring? → unresolved, root gap #2.
- Who owns an athlete/coach record when multiple orgs can upload/edit? Dedupe/correction handling? → open in E2E doc as Q2, now sharper.
- What athlete-level data can each org see vs. publish, and who approves publication? → open in E2E doc as Q10/Q11, now sharper.
- What data/status passes between Team Prep and GMS at long-list/nomination/quota stages? → open in E2E doc as Q6, unchanged.
- What evidence/review is mandatory for grant requests, tranche releases, utilisation? → net new, root gap #3.

**Richa's use cases are still not captured.** Meeting notes explicitly flag her input as pending — get this before the role/permission model is finalized, and test her scenarios against this table before locking anything.

## Design decisions (2026-09-28/29) — resolves root gap #1

Streamlined permission/org flow, proposed and confirmed:

1. **Master Admin → State**: activation grants State a **feature ceiling** (subset of all platform resources/actions), credentials issue after the grant — same sequencing already used for Stakeholders.
2. **State → Federation / Directorate**: State holds its full ceiling and can create exactly these two credential categories. Each grant is a subset of State's own ceiling — subtractive delegation, same "scope granted, never earned" pattern as BMS/Ticketing RBAC. No further re-delegation below this level.
3. **Unified resource × action matrix, one carve-out**: every capability (view-aggregate, view-named, upload/edit, tag, request, approve/sanction, release, publish) is a delegable action on a resource (Roster, Achievers, Grants/Disbursement, Long List, History) — no org-type gets special-cased logic. **Exception: public-page visibility (which sections/figures show) and achiever ownership/featuring are never delegable — always retained by State**, even if Federation/Directorate hold other actions on those same resources. This corrects the earlier build, where Directorate owned both.
4. **Grant lifecycle actions are ordinary actions, not a special toggle**: request / approve-sanction / release all sit on the Grants/Disbursement resource like anything else. State holds all of them by default (nothing delegated yet = State does everything). State typically delegates "request" to Federation and can *optionally* also delegate "approve/sanction/release" to Directorate — but doesn't have to. **This resolves the fallback-approver question cleanly: if State never delegates approval to Directorate (or Directorate doesn't exist in that state), State is simply still holding that action itself — no special case needed.** Confirmed by the user: "State is the primary approver, they can share that permission with the directorate if they want."
5. **Budget request is uncapped at request time**: Federation can submit a request with reason + supporting document regardless of remaining pool balance. The balance check only applies when someone holding "approve/sanction" acts on it.

### What this resolves vs. what's still open (updated)

**Resolved:** root gap #1 (permission model) — now a single unified matrix with one carve-out, no per-org-type special logic. Root gap #4 partially — public-page ownership is unambiguous (State-only), but full "approve before publish" workflow (auto-publish on toggle vs. per-item review) is still undefined.

**Still open, unchanged:**
- Root gap #2 (org-relationship model) — this flow only formalizes State→Federation/Directorate. National federation ↔ state federation, Olympic association, sports authority/department as distinct org types still aren't modeled; routing them through the existing cross-tenant Stakeholder mechanism is a different system today, not unified with this one. Two fixed categories at state level is narrower than "org type shouldn't determine capability" — a scoped decision, not a full solve of S3/P5.
- Root gap #3 (utilisation/evidence) — request-time supporting document is new and good, but post-spend utilisation certificate is still undesigned.
- Root gap #5 (event-agnostic prep) — untouched.
- P1 (sport-wise budget) — Budget Pool is still state-wide, not split per sport; requests are sport-scoped by proxy only.
- Richa's scenarios — still not captured; retest against this updated model once available.

## Next steps (as of 2026-09-27)

- Get Richa's scenarios, retest against this gap table.
- Solve root gaps #1 and #2 first (permission model + org-relationship model) — everything else depends on them.
- Then #3 (utilisation/evidence) and #4 (publish-approval) can proceed in parallel.
- #5 (event-agnostic prep) can wait until GMS's own quota/nomination mechanism firms up (already flagged as future scope in TMS Sports Library PRD).

---

## Appendix: raw meeting notes (2026-09-26)

### 1. Product direction discussed
The current public page is largely static. The proposed flow would let an administrator activate a state, issue credentials, and grant access to selected data and features. A state or authorised organisation could then upload and manage athlete and coach data, view relevant dashboards, create sport-specific associations, prepare event rosters, and manage grant requests. Approved data could populate a dynamic public page.

Design principle: An organisation's name or type should not, by itself, determine what it can do. The product needs configurable permissions for each organisation, sport, dataset and workflow.

### 2. Use cases to turn into a checklist

**Sachin's use cases and operating distinctions**
- Separate team preparation from funding. A sport association may manage athletes, training camps, preparation and team-related work, while a directorate or department handles funding. A directorate should not automatically be treated as the body representing athletes at an event.
- Support grant accountability. After receiving funds—for example, for a training camp—an association must be able to submit expenditure details and a utilisation certificate to the funding body.
- Model national and state sport bodies without collapsing them into one hierarchy. National federations may manage or receive reporting from their state counterparts. Confirm the precise reporting relationships needed for each sport and event.
- Do not require a directorate in every state's team-preparation flow. Its role differs by state and may be financial rather than operational.
- Keep "state" as the geographic/data scope, distinct from the organisation given credentials. The authorised organisation could vary by client or state; its access should be explicitly granted rather than inferred from a fixed label.

**Pratish's use cases and questions**
- Make grants sport-wise, not only district-wise. A Meghalaya sport association requesting funds from the relevant state body needs a workflow tied to its sport and organisation; district breakdowns alone do not describe that request.
- Distinguish requested, sanctioned and disbursed amounts. Sanctioning an amount does not mean it has all been paid. Record each tranche, its amount, date and remaining balance.
- Track utilisation against payments. The receiving organisation should report how each released amount was spent, with supporting details and documents.
- Allow preparation for events beyond the National Games. Rosters, tracking and permissions should work for other national or international events too, without an event-specific workflow baked into the product.
- Resolve the organisation hierarchy before implementing it. The relationship among the state-level account, departments/directorates, Olympic associations and sport associations is not the same in every scenario.

**Richa's use cases — input pending**
- Capture Richa's specific scenarios, exceptions and required permissions in writing or in a follow-up discussion.
- Test those scenarios against the checklist below before the role and permission model is finalised.
- Richa's points were invited at the end of the available recording, but her response is not captured here. No use case has been attributed to her without confirmation.

### 3. Flows demonstrated or proposed that need validation
- Onboarding: Activate a state, assign the appropriate organisation account, generate credentials and choose its initial permissions.
- Data management: Allow an authorised state body or sport association to upload or manage permitted athlete and coach records. Apply sport and state restrictions to both listings and dashboard totals.
- Data visibility: Independently control access to individual athlete details, aggregate figures, uploaded records and historical games data. Confirm which fields may appear on the public page.
- Achievers: Permit an authorised user to tag athletes—for example, by level of achievement—and manage the information intended for public display.
- Event preparation: Select eligible people from the common repository, build a sport/event roster or long list, and pass the required data into the GMS nomination flow. Confirm where quota checking and additional event-specific details belong.
- Grants: Allow an authorised organisation to submit a request with a reason and supporting documents; allow the responsible funding body to review it, sanction an amount, release it in one or more tranches, and track utilisation.
- Public page: Populate state, sport, district and athlete summaries from approved data rather than static content, with controls over which sections and figures are published.

### 4. Inconsistencies and gaps in the present flow
1. "State" is doing two jobs. It refers both to the geographic scope of data and to a potential account holder or decision-maker. These must be separate concepts so the account can be assigned to the appropriate body without changing the state's data boundary.
2. A single fixed hierarchy will not cover the examples discussed. The involvement of a directorate, sports authority, Olympic association and sport association varies. A required directorate step would create the wrong route where that body is not involved.
3. The grant demo does not yet fully represent financial status. A request, a sanction and a payment are different events. Partial disbursement, multiple tranches, balances and utilisation need distinct records.
4. District-based views do not cover sport-led requests. An association's grant, roster and athlete access must be scoped to its sport even when the underlying data also has district attributes.
5. Permissions need to govern actions as well as screens. Viewing aggregates, seeing named athletes, uploading or editing data, tagging achievers, creating rosters, requesting grants, approving grants, releasing money and publishing data should be independently grantable.
6. Event preparation must be reusable. A National Games-specific long-list or quota process should not prevent the same repository and roster capability being used for another event.

### 5. Open questions for the team
- For each state/client, which organisation receives the primary account, and who is authorised to grant access to other organisations?
- Which body approves a grant, sanctions it, releases each tranche and receives the utilisation certificate? Can these rights be split among different bodies?
- What reporting relationships are required between national federations, state associations, state departments and Olympic associations? Which are informational, and which confer approval rights?
- Who owns an athlete or coach record when a state and an association can both upload or edit data? How will duplicates and corrections be handled?
- What athlete-level data can each organisation see, and what can be published publicly? Who approves publication?
- What data and status must pass between Team Prep and GMS at long-list, nomination and quota-check stages?
- What evidence and review steps are mandatory for grant requests, tranche releases and utilisation?

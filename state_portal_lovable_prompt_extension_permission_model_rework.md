# Lovable Extension Prompt — Permission Model Rework

_Written 2026-09-29. Supersedes the fixed toggles in `state_portal_lovable_prompt_extension_grants_achievers.md` §2d/§2e and the `Federation.canSubmitGrantRequests/canTagAchievers/listedPublicly` booleans. Reuses the same R1–R5 resource labels already established in `state_portal_lovable_prompt_extension_national_rbac.md` §1, so the in-state grid and the cross-tenant Stakeholder grid read as one system, not two._

Paste into the **same Lovable project**. Do not rebuild anything already working (Global View, Login, Team Preparation, National Games History, Team & Trials, Master Admin's States/Stakeholder Access/National Map tabs, Federation's own dashboard layout). This is a rework of *where permissions live and who owns them*, not a visual redesign — reuse existing card/table/toggle/badge components throughout.

---

## 1. The core change

Permissions are no longer three hardcoded booleans on `Federation` plus a separate Directorate-owned toggle panel. They're a **single delegation grid State controls**, same resource × action shape used everywhere else in this product:

- **Resources (5, same R1–R5 labels as the Stakeholder grid):** R1 Roster, R2 History, R3 Long List, R4 Grants/Disbursement, R5 Achievers.
- **Actions (only the applicable ones show per resource row):**
  - R1 Roster → View-Aggregate, View-Named, Upload/Edit
  - R2 History → View-Aggregate, View-Named
  - R3 Long List → View-Aggregate, View-Named, Create/Manage, Push-to-GMS
  - R4 Grants → View, Request, Approve/Sanction, Release
  - R5 Achievers → View, Tag

**State holds every action by default.** Nothing is pre-granted to a new Federation or Directorate — State explicitly checks the boxes it wants to delegate, per grantee. This replaces the old "on by default" toggles.

**One non-delegable carve-out, shown as its own locked section, not grid columns:** public-page visibility (which of the 3 public tabs render, and Featured Achievers) and achiever *ownership* (deciding what's featured/published) always stay with State. A Federation/Directorate can be granted R5's "Tag" action (they can create an achiever tag) without ever touching what gets featured or published.

**Grant lifecycle has no special case.** Approve/Sanction/Release on R4 are just checkboxes like any other. If State never checks them for a Directorate, State itself reviews and sanctions incoming Grant Requests — same review UI, just rendered on State's own dashboard instead.

---

## 2. State Admin — rework the existing "Manage Access" tab

Keep the existing two lists (Directorate, State Federations) and creation forms exactly as built. Add, per org in each list, a **"Permissions" button** that opens the delegation grid described in §1 — a checkbox table, rows = resource action-sets above, reuse the same toggle-switch visual language already used elsewhere in this prototype. Changes take effect live, same as the existing Directorate toggles used to.

Add two new subsections to this tab, moved here from the old Directorate dashboard (§2d/§2e of the grants/achievers doc — remove them from Directorate entirely):

**2a. Achievers (management)** — identical UI/flow to the old Directorate §2d (pick-from-roster two-step tag flow, custom category creation, edit/remove any achiever in the state regardless of who tagged it). State now owns this outright.

**2b. Public Display** — identical UI to the old Directorate §2e-ii (tab toggles for Grant Sanction Dashboard / Achievers / Associations, Featured Achievers picker). State now owns this outright. Remove §2e-i's Federation Permissions table entirely — it's replaced by the per-org grid above.

**2c. Grants (fallback approver)** — if a Directorate exists but its grid doesn't have R4 Approve/Sanction/Release checked, incoming Grant Requests from Federations route here instead: reuse the exact Directorate Review panel UI (§2b of the grants/achievers doc — sanctioned amount entry, approve/reject, tranche scheduling) verbatim, just rendered under State. If a Directorate *does* hold those actions, this section shows a simple "Delegated to Directorate" state instead, no duplicate queue.

---

## 3. Directorate Dashboard — trim to match its actual grant

Keep 2a (Budget Pool) and 2c (Disbursement Tracker — but only if R4 Release is checked) as-is. **Remove 2d (Achievers) and 2e (Permissions & Display) entirely** — moved to State per §2. 2b (Grant Requests/Review) only renders if R4 Approve/Sanction is checked for this Directorate; otherwise show "Reviewed by [State Name]" instead of an empty section.

---

## 4. Federation Dashboard — small adjustments

**3c Request Budget:** make the **supporting document upload required**, not optional (file picker, dummy accept). Requested amount has **no validation against remaining pool balance** — a request can exceed what's currently in the pool; only the Directorate/State review screen enforces the cap, at sanction time. Every other part of §3c stays as-is.

Everything else in the Federation dashboard (3a, 3b, 3d, 3e, 3f) is ungated by this rework, except: whichever R1/R3/R4/R5 actions this Federation's grid doesn't have should hide the matching section/button (e.g., no R3 Push-to-GMS → hide that action on Team & Trials; no R5 Tag → hide "+ Tag Achiever"), same conditional-render pattern already used for the old boolean toggles.

---

## 5. Mock data model changes

Replace:
```
Federation { ...canSubmitGrantRequests, canTagAchievers, listedPublicly... }
StatePortalSettings { ...showGrantSanctionDashboard, showAchievers, showAssociations... }
```
with:
```
PermissionGrant {
  id, stateId, granteeType: "Federation" | "Directorate", granteeId,
  resource: "R1" | "R2" | "R3" | "R4" | "R5",
  actions: string[]   // subset of that resource's applicable action list, §1
}

StatePublicSettings {          // renamed from StatePortalSettings, now explicitly State-only, no grantee
  stateId,
  showGrantSanctionDashboard: boolean,
  showAchievers: boolean,
  showAssociations: boolean,
  featuredAchieverIds: string[]
}
```
`Federation.listedPublicly` becomes a plain field State edits directly in §2b's Associations list, not a delegated permission.

Seed data: give the existing seeded Directorate (Meghalaya) R4 = [View, Approve/Sanction, Release] so the demo still shows the full Directorate flow live; give one other seeded state's Directorate an **empty** R4 grant so its Grant Requests visibly route to State instead — this is the scenario worth demoing explicitly, since it's the model's main behavioral change.

---

## 6. What stays untouched

Global View, Login, Team Preparation tab, National Games History tab, Team & Trials tab, Master Admin's States/Stakeholder Access/National Map tabs, the public portal pages' visual design, and the Federation/Directorate dashboard layouts beyond the specific removals/additions above.

Build with the same working mock-data interactivity standard as the rest of this prototype: checking/unchecking a grid action actually hides/shows the corresponding UI live, on the correct login, with no reload.

# Nomenclature

Grouping entity is called a **Squad** (e.g. "Create Squad," "My Squads," squad status) rather than literally "Bunch" — reads naturally in a sports context (trial squad, probable squad). "Team & Trials" stays as the tab name.

---

# Lovable Extension Prompt — Team & Trials (Squads → Export / Send to GMS)

Paste this into the **same Lovable project**. Add a new third tab, **Team & Trials**, next to the existing Team Preparation and National Games History tabs in the State View. Don't touch those two tabs, the Global View, or the Login flow. Match the existing visual style exactly (same accent color, card style, spacing, type scale) and reuse the existing Athlete Listing table/filter component wherever noted below — don't rebuild it from scratch.

---

## 1. New tab: Team & Trials (`/state/:stateName`, third tab)

**Header:** Tab title "Team & Trials" with a one-line description: "Shortlist your athletes into a Squad, then export it or send it as your Long List to a GMS event." Top-right: a primary **"+ Create Squad"** button.

**Main content — Squad list:** a grid or list of Squad cards, one per squad this state has created. Each card shows:
- Squad name
- Sport/discipline tag
- Athlete count
- Status badge: **Draft** (gray) or **Sent to GMS** (accent color, with the event name, e.g. "Sent · 39th National Games")
- Created date

Clicking a card opens that Squad's detail view. Empty state (no squads yet): friendly empty state with the "+ Create Squad" CTA front and center.

---

## 2. Create Squad flow

Triggered by "+ Create Squad" — a modal or dedicated step:

1. **Name the squad** (text input, e.g. "Archery Trials – Sept 2026").
2. **Pick a primary sport/discipline** (dropdown, from the state's existing sport list).
3. **Add athletes** — reuse the existing Athlete Listing table + filter bar exactly as built (same columns: Sport, Athlete Name, Contact ID, DOB/Age, Gender, District; same district/sport/gender/age filters; same filter-chip bar), but as a **picker**: add a checkbox column on the left, and a persistent selection tray/footer showing "Selected: N athletes" with the running count as filters are applied and checkboxes toggled. Default-filter the table to the squad's chosen sport (removable, like any other filter chip) so the common case is fast, but don't hard-lock it — a squad can pull athletes across disciplines.
4. **"Add Selected to Squad"** button creates the squad in **Draft** status and opens its detail view.

---

## 3. Squad Detail view

- **Header:** squad name (inline-editable), sport/discipline tag, status badge, created date.
- **Athlete table:** same columns as the Athlete Listing (Sport, Athlete Name, Contact ID, DOB/Age, Gender, District), one row per athlete in the squad, each with a **Remove** action.
- **"+ Add More Athletes"** button reopens the same picker from step 3 above, pre-excluding athletes already in the squad.
- **Bottom action bar**, two buttons:
  - **Export as CSV** — generates and downloads a real CSV client-side (squad name as the filename, same columns as the table). This should actually work, not just look real — it's a small enough operation to implement for real rather than fake.
  - **Send to GMS Event** — opens a picker modal listing mock GMS events (see data model below). Selecting an open event and confirming:
    - Sets the squad's status to **Sent to GMS**, tagged with that event's name.
    - Shows a small confirmation line: "This squad is now the Long List for [Event Name]. Discipline nomination under quota happens in GMS." (context copy only — GMS nomination itself is out of scope for this app).
    - Once sent, the squad becomes **view-only**: hide Remove/Add More/Export-to-send actions, keep the athlete table visible, and show a "Read-only · Sent" badge near the header. (Export as CSV can stay available even after sending.)

Events whose `longListStatus` is `"Closed"` appear in the picker but are disabled/grayed out with a "Closed" label, not selectable.

---

## 4. Mock data

```
Squad {
  id, name, sport, stateId,
  athleteContactIds: string[],   // references RegisteredAthlete.contactId
  status: "Draft" | "Sent",
  gmsEventId?: string,
  createdAt
}

GmsEvent {
  id, name,                      // e.g. "39th National Games"
  hostState,                     // e.g. "Meghalaya"
  edition,                       // e.g. 39
  longListStatus: "Open" | "Closed"
}
```

Seed 3 mock `GmsEvent` records:
- **39th National Games — hosted by Meghalaya**, `longListStatus: "Open"` (the flagship, always demo this one).
- **38th National Games — hosted by Goa**, `longListStatus: "Closed"` (so the picker visibly shows both states).
- One more open event in a different sport-heavy context if useful for variety.

Seed 2–3 mock `Squad` records per active state so the Team & Trials tab isn't empty on first load — mix of Draft and Sent statuses so both card treatments are visible without any manual clicking during a demo.

---

Do not change the Team Preparation tab, National Games History tab, Global View, or Login flow — this is purely additive: a new third tab, reusing the existing Athlete Listing table as a selection picker, plus the Squad create/detail/export/send flow described above.

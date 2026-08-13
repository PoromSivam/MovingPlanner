# PRD — FitCheck (working title)

**A constraint checker for small-apartment moves.**
Tell it your room and your furniture. It tells you what fits, what breaks, and what won't make it through the door.

| Field | Value |
|---|---|
| Status | Draft v0.1 — for iteration |
| Owner | psk |
| Last updated | 2026-08-12 |
| Working title | FitCheck (alternates: Doorway, Snug, Tape) |

---

## 1. Overview

Moving into a studio or a smaller apartment is a **constraint satisfaction problem that people solve by guessing**, and they only find out they guessed wrong on move-in day, when the truck is double-parked and the recliner is stuck in the doorway.

FitCheck is not an interior design tool. It does not make pretty renders. It answers three questions with numbers:

1. **Does my furniture fit in this room, with usable walkways?**
2. **Will each piece physically get from the truck to the room?**
3. **What do I have to sell, store, or leave behind — and by when?**

The output is a validated 2D layout plus a dated move packet you can hand to a mover.

---

## 2. Problem

Five failures, in the order they cost you money:

| # | Failure | Why it happens | Cost |
|---|---|---|---|
| P1 | Furniture doesn't fit the room | Listing floor plans are marketing art with no dimensions | Buy replacements, or live cramped |
| P2 | Furniture fits but the room is unusable | Nobody checks clearances — aisles, door swings, recline depth | Daily friction for the length of the lease |
| P3 | Furniture won't fit through the door, corridor, or elevator | Nobody measures the path, only the destination | Item abandoned on move day, or a return fee |
| P4 | Downsizing decisions made emotionally, too late | No objective "this does not fit" signal until it's in the truck | Storage unit rented in a panic (~$100+/mo, indefinitely) |
| P5 | Building logistics missed | COI, freight elevator windows, move-in fees are buried in the lease | Turned away at the loading dock, rescheduled move |

The common root cause: **the constraints are invisible until they're violated.**

---

## 3. Where this came from

Origin research is a real session (2026-08-12): planning a move into a long, narrow studio — bathroom and closet at the entry, galley kitchen running the entire long wall, glass and a balcony at the far end. Furniture to place: L-shaped desk, 77" TV on a floating stand, 3-drawer dresser, La-Z-Boy recliner, queen bed and nightstand.

What actually happened over three iterations, and every one of these is a product requirement in disguise:

- **The floor plan had no dimensions.** Everything had to be estimated from image proportions. → *FR-1 (scale calibration).*
- **A wrong assumption about the kitchen run invalidated the whole layout.** The kitchen ran the full wall, which killed the seating position. → *FR-4 (constraint re-validation on every edit).*
- **The room's usable width was not its width.** 13 ft wide minus a 4 ft kitchen aisle = a 7 ft furniture band. That single derived number decided the entire layout. → *FR-5 (usable-area derivation).*
- **The 77" TV needed ~10.4 ft of viewing distance**, which the room could not provide across its width. A rule, not an opinion. → *FR-6 (rule library).*
- **The recliner was flagged as the piece most likely to fail at the door** — many models are 38–40" deep and won't clear a 32" door unless the back detaches. → *FR-7 (path check).*
- **After three redraws, the real blocker was six missing measurements.** → *FR-2 (measure mode).*

That last one is the product thesis: **people will iterate on layouts forever to avoid spending ten minutes with a tape measure.** The app's job is to make the measuring feel like progress.

---

## 4. Users

**Primary — the constrained renter.** Moving into 300–700 sq ft, on a lease, bringing existing furniture they can't easily replace. Motivated by loss avoidance, not aesthetics. Will not model a whole apartment in 3D.

**Secondary — the downsizer.** Moving from a larger place. The core question is *what do I get rid of*, and they need an objective answer to settle it (often with a partner or family).

**Tertiary — the remote/short-term mover.** Signing a lease on a unit they have never physically stood in, working only from listing photos and a floor plan image.

**Explicitly not the user (v1):** homeowners renovating, interior designers, anyone who wants a photoreal render.

---

## 5. Jobs to be done

- When I'm deciding between two units, I want to know which one my life actually fits into, so I don't sign a 12-month lease on a mistake.
- When I'm packing, I want an objective keep/sell/store call on each large item, so I stop relitigating it.
- Before move day, I want to know which pieces are at risk in the doorway, so I can disassemble them in advance instead of on the sidewalk.
- On move day, I want the movers to know where each item goes without asking me forty times.

---

## 6. Competitive landscape and the wedge

| Tool | What it's for | Why it doesn't solve this |
|---|---|---|
| Planner 5D, RoomSketcher, Roomstyler | Design and render | Aesthetic output; clearances are decorative, not enforced |
| IKEA Kreativ / catalog planners | Selling that vendor's catalog | Can't model furniture you already own |
| MagicPlan, CamToPlan | Capturing floor plans | Produces a plan, then stops. No furniture reasoning |
| Graph paper, Excel, a chat assistant | Everything | Manual, no persistence, no re-validation, no path check |

**The wedge: nobody enforces constraints, and nobody checks the path.** Every competitor lets you place a sofa in a 20" walkway and says nothing. And no consumer tool asks whether the sofa fits through the front door — which is the failure that costs the most and is the easiest to compute.

**Second wedge: inventory-first, not room-first.** Every existing tool starts with "draw your room." FitCheck starts with "list what you own," because that list is the thing the user is actually anxious about.

---

## 7. Non-goals (v1)

- 3D or photoreal rendering
- AR / LiDAR room capture (v2 candidate)
- A furniture marketplace or affiliate catalog
- Multi-room whole-home planning (v1 is one room)
- Automatic wall detection from a photo via ML (v1 uses guided manual tracing)
- Collaboration, comments, multi-user editing
- Mover marketplace or booking

---

## 8. Product principles

1. **Numbers over renders.** A violation message with an actual figure beats a pretty picture.
2. **The tape measure is the product.** Every screen should make the next measurement feel cheap.
3. **Refuse to be wrong silently.** Any dimension the user did not measure is marked as an estimate and carries a visible confidence flag, everywhere it's used.
4. **One room, done properly.** Depth before breadth.
5. **Exportable.** The artifact leaves the app — mover packet, layout PDF, shareable link.

---

## 9. Scope

### v0.1 — "Does it fit" (target: one weekend of build time)

The smallest thing that delivers the core insight.

- Manual room entry: rectangle, width × length, ceiling height
- Add furniture from a starter library or custom (name, W × D × H)
- Drag-and-drop 2D top-down placement, snap to walls, rotate in 90° steps
- Live clearance checking against the rule library, with violations listed in plain language
- Door/path check: does each item pass through a given opening
- Persist locally (browser storage), no accounts

**Definition of done:** the studio from Section 3 can be modeled and every conclusion from that session is reproduced by the app without human reasoning.

### v1 — "Move packet"

- Accounts and cloud persistence
- Non-rectangular rooms (L-shape, alcoves), fixed features: doors with swing arcs, windows, kitchen runs, closets, columns
- Floor plan image import with two-point scale calibration, traced over
- Measure mode: guided checklist of the measurements that matter, with confidence flags
- Furniture inventory with photos, condition, and keep/sell/store status
- Path check extended to full route: door → corridor turn → elevator → unit door
- Move packet export (PDF): layout, labeled inventory, per-item destination, at-risk items, dated task list
- Building logistics checklist generated backwards from the move date

### v2 — candidates, to be prioritized after v1 signal

- Auto-layout: generate and rank valid layouts from inventory + room (constraint solver, then LLM for tie-breaking and explanation)
- Unit comparison: model the same inventory against 2–3 candidate apartments, score them
- Phone camera / LiDAR room capture
- Furniture dimension lookup by brand + model (scraped or user-contributed)
- Native mobile app (measuring happens on-site, on a phone)
- Shared link for a partner or roommate to review

---

## 10. Functional requirements

### FR-1 — Room definition
- **FR-1.1** Enter a rectangular room by width, length, ceiling height. Units toggle ft/in ↔ metric, stored internally in millimeters.
- **FR-1.2 (v1)** Add fixed features: door (with hinge side and swing direction), window, sliding door, kitchen counter run, closet, column, radiator.
- **FR-1.3 (v1)** Import a floor plan image; user marks two points and enters the real distance between them to set scale; user traces walls over the image.
- **Acceptance:** a traced plan renders to scale, and a known 5 ft dimension measures 5 ft ± 3% in the app.

### FR-2 — Measure mode
- **FR-2.1** Guided checklist of the measurements the layout actually depends on, generated from the current model rather than a fixed list.
- **FR-2.2** Every dimension is tagged `measured` or `estimated`. Estimated values render with a distinct visual treatment wherever they appear.
- **FR-2.3** The layout shows a confidence banner while any load-bearing dimension is still estimated.
- **Acceptance:** exporting a move packet while critical dimensions are estimated produces a visible warning on the export itself.

### FR-3 — Inventory
- **FR-3.1** Add items with name, W × D × H, quantity, optional photo, optional category.
- **FR-3.2** Starter library of common items with default dimensions (queen bed 60×80, US door 32" clear, etc.) that the user can override.
- **FR-3.3** Per-item flags that change the rules applied: `reclines`, `has_doors`, `has_drawers`, `disassembles`, `wall_mounted`.
- **FR-3.4 (v1)** Status per item: `keep` / `sell` / `store` / `undecided`, with a filtered "not yet decided" view.
- **Acceptance:** a recliner flagged `reclines` triggers the rear-clearance rule; unflagging it removes the violation.

### FR-4 — Layout canvas
- **FR-4.1** Top-down 2D canvas, drag to place, rotate in 90° steps (v1: free rotation), snap to walls and to other items.
- **FR-4.2** Constraints re-validate on every mutation, not on a "check" button.
- **FR-4.3** Violations render both on the canvas (highlighted region) and as a plain-language list.
- **FR-4.4** Undo/redo. Named layout variants so alternatives can be compared side by side.
- **Acceptance:** changing a single wall dimension re-runs every rule and updates every violation within 100 ms.

### FR-5 — Derived usable area
- **FR-5.1** Compute and display *usable* floor area = total area minus required circulation, work aisles, and door swing arcs.
- **FR-5.2** Surface the derived furniture band width, not just the room width.
- **Rationale:** the origin session turned on this number. A 13 ft room with a full-length kitchen is a 7 ft room.

### FR-6 — Constraint rule engine
- **FR-6.1** Rules are data, not code — a versioned rule library (see Section 11) so rules can be added without a release.
- **FR-6.2** Each violation returns: rule id, severity (`blocker` / `warning` / `note`), the actual value, the required value, and a one-line fix suggestion.
- **FR-6.3** A user can dismiss an individual violation with a reason; dismissals persist and appear in the export as accepted trade-offs.
- **Acceptance:** every violation message contains a number. No message reads "this may be tight."

### FR-7 — Path check ("will it get in")
- **FR-7.1** Define a route as an ordered list of constrictions: building door, corridor width, corridor turn, elevator door, elevator interior (W × D × H), unit door, interior doors.
- **FR-7.2** For each item, test its smallest cross-section against each constriction, including tilted and rotated orientations.
- **FR-7.3** For corridor turns, apply the standard turn test using corridor width, turn width, and item length and thickness.
- **FR-7.4** Items that fail are flagged `at risk` with the specific constriction and the required action (disassemble, remove back, hoist, leave behind).
- **Acceptance:** a 40" deep recliner against a 32" door returns a blocker naming the door, not a generic warning.

### FR-8 — Move packet export
- **FR-8.1** PDF containing: scaled layout with numbered items, inventory table with destination per item, at-risk item list with required actions, and a dated pre-move task list.
- **FR-8.2** Task list generated backwards from the move date (COI request, freight elevator booking, utility transfer, disassembly, address change).
- **FR-8.3 (v1)** Shareable read-only web link.

---

## 11. Rule library (the actual IP)

Starting set. Values are industry rules of thumb, not code; each rule carries its source and can be overridden per project.

| ID | Rule | Required | Severity |
|---|---|---|---|
| CLR-01 | Primary walkway width | 36" preferred, 30" minimum | blocker under 30" |
| CLR-02 | Secondary / single-person path | 24" minimum | warning |
| CLR-03 | Kitchen work aisle, one cook | 42" preferred, 36" minimum | blocker under 36" |
| CLR-04 | Bedside clearance | 30" preferred, 24" minimum | warning |
| CLR-05 | Clearance at the foot of a bed | 24" minimum | warning |
| CLR-06 | Door swing arc kept clear | radius = door leaf width | blocker |
| CLR-07 | Drawer / cabinet pull-out clearance | drawer depth + 6" | warning |
| CLR-08 | Recliner rear clearance | 12–18" standard, 4–6" wall-hugger | blocker if `reclines` |
| CLR-09 | Desk chair pull-out | 36" behind the desk edge | warning |
| CLR-10 | Sofa to coffee table | 14–18" | note |
| TV-01 | Viewing distance, SMPTE 30° reference | `d ≈ 1.87 × screen_width` (≈ 1.63 × diagonal) | warning |
| TV-02 | Viewing distance, THX max 40° | `d ≥ 1.37 × screen_width` | warning if closer |
| TV-03 | Wall mount backing | flag concrete/masonry walls for anchor type and landlord permission | note |
| PTH-01 | Straight passage | item's smallest cross-section < opening width − 1" | blocker |
| PTH-02 | Corridor turn | standard turn test on corridor width, turn width, item length and thickness | blocker |
| PTH-03 | Elevator interior | item fits within cab W × D, or diagonal if tiltable, and under cab height | blocker |
| ENV-01 | Bed / upholstery against exterior glass | note condensation and sun exposure | note |
| ENV-02 | Desk facing a window | note glare and backlight on video calls | note |

Worked example for TV-01: a 77" 16:9 panel is 67.1" wide → `d = (67.1 / 2) / tan(15°) = 125"` ≈ **10.4 ft**. This is the number that made the origin layout impossible across a 13 ft room with a 4 ft kitchen aisle.

---

## 12. Data model (sketch)

```
Project            id, name, move_date, address_label, unit_system, created_at
Room               id, project_id, polygon[], ceiling_height_mm, confidence
Feature            id, room_id, type(door|window|slider|counter|closet|column),
                   geometry, swing_direction, hinge_side, confidence
InventoryItem      id, project_id, name, w_mm, d_mm, h_mm, qty, photo_url,
                   flags[], status(keep|sell|store|undecided), confidence
Placement          id, layout_id, item_id, x_mm, y_mm, rotation_deg
Layout             id, project_id, name, is_primary
Route              id, project_id, name
Constriction       id, route_id, order, type, w_mm, h_mm, d_mm, turn_geometry
Rule               id, version, code, params, severity, source_note
Violation          id, layout_id, rule_id, subject_ids[], actual, required,
                   severity, suggestion, dismissed_reason
```

Store every dimension in **millimeters as integers**. Display units are a presentation concern. Mixing feet-inches and metric in storage is the single most reliable way to ship a rounding bug into a rule engine.

---

## 13. Architecture

**v0.1 — ship it, don't architect it**
- Next.js + TypeScript, one app, no backend
- `react-konva` for the 2D canvas (mature, handles drag/snap/hit-testing)
- Zustand for state; rules run as pure functions over state
- `localStorage` persistence, JSON import/export
- Deploy on Vercel

**v1**
- Supabase: Postgres, auth, storage for floor plan images and item photos
- Rule engine extracted into `packages/rules` — pure, dependency-free, exhaustively unit tested. This is the asset; keep it portable.
- PDF export via `react-pdf` or server-side Puppeteer
- Responsive web first, since measuring happens on a phone in an empty apartment

**v2**
- Auto-layout as a separate service. Constraint solver first (deterministic, testable); LLM only for ranking and explaining, never for computing geometry.
- Expo/React Native only if usage data proves the phone is where the work happens.

**Architectural rule:** geometry and rule evaluation never live in a React component. Pure functions, in one package, with tests. Everything else is replaceable.

---

## 14. Success metrics

**North star:** projects that reach an exported move packet with zero unresolved blockers.

| Metric | Target (first 100 users) |
|---|---|
| Activation: room + 3 items placed | 60% of signups |
| Magic moment: first real violation surfaced | 80% of activated users, within session 1 |
| Measure-mode completion | 35% of projects |
| Path check run | 50% of projects |
| At-risk items caught before move day | tracked qualitatively via follow-up |
| Retention: returns for a 2nd session | 40% |

**The one qualitative signal that matters:** a user reports the app caught something that would have cost them money.

---

## 15. Risks

| Risk | Severity | Mitigation |
|---|---|---|
| Users won't measure, so outputs are garbage | High | Confidence flags everywhere; measure mode framed as progress; app works with estimates but says so loudly |
| Rule library is subtly wrong and users trust it | High | Every rule cites a source; severities are conservative; blockers only where the physics is unambiguous |
| Scope creep into a 3D design tool | High | Non-goals section is binding; any 3D request goes to the v2 backlog |
| One-and-done usage — people move rarely | Medium | Not a retention product; it's a high-intent, high-value moment. Monetize the moment, not the habit. Unit comparison brings them back during the search phase |
| Floor plan tracing is fiddly on mobile | Medium | v0.1 skips it entirely — manual rectangle first |
| Building-logistics content varies by city | Low | Generic checklist v1; localize later if it matters |

---

## 16. Open questions

1. Is the entry point **"I'm moving"** or **"I'm choosing between apartments"**? The second has more urgency and more competition between units to compare — it may be the better wedge.
2. Free vs paid: one-time fee per project, or free with a paid export? A move is a high-willingness-to-pay moment.
3. Does the furniture dimension library need to be seeded, or is manual entry acceptable friction for v1?
4. Should the L-shaped desk / sectional case be modeled as a real polygon or as two rectangles glued together? (Two rectangles is far simpler and probably sufficient.)
5. Name.

---

## 17. Repo layout

```
/
├── README.md
├── PRD.md                  ← this file
├── docs/
│   ├── rules.md            ← rule library with sources
│   └── decisions/          ← ADRs, one file per architectural decision
├── apps/web/               ← Next.js app
└── packages/rules/         ← pure geometry + constraint engine, no UI deps
```

**First three issues to open:**
1. `feat: room + item model, mm-based, with unit conversion at the display layer`
2. `feat: 2D canvas with drag, snap-to-wall, and 90° rotation`
3. `feat: rule engine v0 — CLR-01, CLR-03, CLR-06, CLR-08, PTH-01`

---

## 18. Changelog

- **2026-08-12** — v0.1 draft. Problem, scope, rule library, architecture.

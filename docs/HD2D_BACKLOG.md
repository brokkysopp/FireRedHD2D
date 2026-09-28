# HD-2D Implementation Backlog

GitHub Issues are currently disabled on this fork, so this file is the working backlog until Issues are enabled.

## HD2D-001 — Centralize renderer configuration

**Goal:** Create a renderer-focused configuration structure while preserving current visual output and behavior.

**Scope**
- camera pitch
- perspective strength
- camera distance/focal relationship
- wall height
- tree height
- margin fade
- wood/depth shading
- tilt shift
- current environment variables as overrides where practical

**Acceptance criteria**
- Default behavior matches the current renderer.
- Existing environment variables continue to work or have documented equivalents.
- No gameplay/emulation logic is changed.
- `cargo fmt --check`, `cargo clippy --all-targets -- -D warnings`, and `cargo test` pass.

---

## HD2D-002 — Camera configuration and presets

**Goal:** Replace hard-coded projection values with a configurable camera.

**Support**
- pitch
- yaw
- distance
- focal length
- look/target offset
- vertical composition offset

Add a small set of runtime presets for art-direction comparison.

**Acceptance criteria**
- One preset exactly reproduces the current default camera.
- No actor/world-position authority moves into renderer code.
- Existing regression suite still passes.

---

## HD2D-003 — Actor grounding and contact shadows

**Goal:** Improve player/NPC grounding while preserving current actor extraction.

**Scope**
- subtle contact shadow
- stable feet pivot
- readability when scenery occludes the player
- optional cosmetic interpolation only if authoritative positions remain untouched

**Acceptance criteria**
- Tall-grass/field effects still correctly cover character legs.
- Actor edge/culling regression behavior remains intact.

---

## HD2D-004 — Building presentation pass

**Goal:** Improve buildings while retaining FireRed facade art.

**Scope**
- roof depth and ridge consistency
- side/rear geometry
- eaves
- facade preservation
- doors/windows/signage readability
- reduced screen-edge shearing

**Acceptance criteria**
- Pallet and Viridian verified building families remain stable.
- Generic rules are preferred over location-specific exceptions.

---

## HD2D-005 — Layered FireRed tree treatment

**Goal:** Prototype a layered/sliced tree presentation using original artwork.

**Acceptance criteria**
- Route 1 tree regression cases remain valid.
- Player visibility behind tree crowns is not worsened.
- Default art remains recognizable as FireRed.

---

## HD2D-006 — 2.5D tall grass

**Goal:** Add depth to tall grass without touching encounter logic.

**Acceptance criteria**
- FireRed remains authoritative for encounter behavior.
- Existing grass field effects remain visible and correctly depth-sorted.
- Route 1 regression suite passes.

---

## HD2D-007 — Water presentation

**Goal:** Add restrained depth/motion to water while preserving the pixel-art palette.

**Acceptance criteria**
- No photorealistic/PBR styling.
- Water transitions do not interfere with collision or map logic.

---

## HD2D-008 — Data-driven interior profiles

**Goal:** Move verified interior furniture geometry toward reusable profiles without losing current special cases.

**Acceptance criteria**
- Oak's Lab, Viridian Center, and Viridian Mart retain verified behavior.
- Profiles can describe identity/pattern, dimensions, geometry type, height, and depth.

---

## HD2D-009 — Interior wall/depth consistency

Standardize room walls, shelves, counters, desks, machinery, and boundary treatment.

---

## HD2D-010 — Deterministic visual screenshot audit

Build on the existing headless and ROM-layout tooling to produce deterministic flat-vs-HD2D screenshots for review.

---

## HD2D-011 — Kanto renderer report

Generate a report that marks layouts as PASS, REVIEW, or unsupported/special-case without committing ROM-derived assets.

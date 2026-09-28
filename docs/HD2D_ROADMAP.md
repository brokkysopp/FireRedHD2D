# FireRed HD-2D Roadmap

## Objective

Build a polished HD-2D presentation layer on top of the existing FireRed-aware GBA emulator without changing FireRed gameplay authority.

The project starts from the existing `--3d` renderer and its regression/audit tooling.

## Milestone 0 — Project baseline

- [x] Fork upstream repository
- [x] Create `hd2d-development` branch
- [x] Add agent development rules
- [ ] Confirm a local Windows build
- [ ] Confirm a legally obtained US FireRed ROM launches with `--3d`
- [ ] Capture baseline screenshots for Pallet Town, Route 1, Viridian City, Center, and Mart

### Local baseline commands

```sh
cargo build --release
cargo test
cargo clippy --all-targets -- -D warnings
cargo fmt --check

./target/release/gba path/to/firered.gba --3d
```

ROM path must come before flags.

---

## Milestone 1 — Vertical slice foundation

Target: Pallet Town → Route 1 → Viridian City → Pokémon Center → Mart.

### HD2D-001 Renderer configuration

Create a renderer-focused configuration structure while preserving current defaults exactly.

Move visual tuning behind one source of truth for:
- camera pitch
- perspective strength
- camera distance/focal relationship
- wall height
- tree height
- margin fade
- wood/depth shading
- tilt shift

Environment variables may remain as overrides initially.

**Acceptance:** default screenshots and behavior remain unchanged.

### HD2D-002 Camera system

Replace hard-coded projection constants with a camera configuration.

Support:
- pitch
- yaw
- distance
- focal length
- target/look offset
- vertical composition offset

Add a few runtime presets for quick art-direction comparison.

**Acceptance:** current camera can be reproduced exactly from configuration.

### HD2D-003 Actor grounding

Improve presentation of player/NPC billboards without changing actor reconstruction.

Add/tune:
- subtle contact shadow
- feet pivot stability
- occlusion readability
- cosmetic movement interpolation only if it does not alter authoritative position

Preserve field effects such as tall grass covering legs.

### HD2D-004 Buildings

Improve outdoor building depth while preserving original FireRed facade art.

Priorities:
- consistent roof depth
- side/rear geometry
- eaves
- doors/windows/signage remain readable
- avoid perspective shearing at screen edges

### HD2D-005 Trees

Create a polished FireRed-art-first tree treatment.

Investigate a layered/sliced billboard approach before replacing trees with fully modeled assets.

Preserve regression behavior for Route 1 tree edges and player visibility.

### HD2D-006 Tall grass

Add 2.5D grass presentation while preserving FireRed encounter logic and field effects.

The original tile art remains the ground/base visual.

### HD2D-007 Water

Add restrained depth and motion to water while preserving the FireRed palette/art style.

Do not pursue photorealistic water.

---

## Milestone 2 — Interior system

Target: Oak's Lab, Viridian Center, Viridian Mart.

### HD2D-008 Data-driven interior profiles

Refactor verified furniture handling into reusable profiles where practical.

Profiles should describe:
- tile/pattern identity
- dimensions
- geometry type
- height/depth
- facade/top treatment

Avoid losing existing verified special cases during refactor.

### HD2D-009 Interior wall/depth consistency

Standardize back walls, shelves, counters, tables, machines, and room boundaries.

---

## Milestone 3 — Visual audit automation

### HD2D-010 Screenshot audit

Create tooling to export deterministic comparison images for known maps/layouts.

Suggested structure:

```text
audit/
  pallet-town/
    flat.png
    hd2d.png
  route-1/
    flat.png
    hd2d.png
  viridian-city/
    flat.png
    hd2d.png
```

### HD2D-011 Kanto renderer report

Build on the existing ROM layout audit to classify layouts as:
- PASS
- REVIEW
- unsupported/special-case

Do not include ROM data or copyrighted extracted assets in commits.

---

## Milestone 4 — First badge coverage

Target:
- Viridian Forest
- Pewter City
- Pewter Gym
- Brock battle and overworld recovery

This milestone validates:
- dense forest rendering
- trainer-heavy areas
- unusual interiors
- battle fallback
- return from battle to HD-2D

---

## Milestone 5 — Broader Kanto coverage

Systematically review cities, routes, caves, gyms, and special buildings.

Favor generic classifier/profile improvements that fix multiple maps at once.

---

## Later — GPU renderer evaluation

Only after scene interpretation is stable, evaluate replacing the software rasterizer with a GPU backend such as `wgpu`.

Potential benefits:
- GPU depth buffer
- shaders
- inexpensive alpha effects
- shadow maps
- water/grass animation
- post processing
- better performance at larger output sizes

This is not an early milestone. The emulator and FireRed world extraction remain authoritative regardless of renderer backend.

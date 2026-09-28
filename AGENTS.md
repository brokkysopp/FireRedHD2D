# FireRed HD-2D Agent Instructions

## Project goal

Turn the existing experimental FireRed `--3d` renderer into a polished HD-2D / 2.5D presentation while keeping the original ROM and emulator authoritative for gameplay.

The visual target is a miniature diorama made from FireRed's original pixel artwork: pixel-sharp textures, restrained near-orthographic perspective, believable depth, subtle lighting/shadows, billboard characters, and no photorealistic/PBR look.

## Critical architecture rule

Do not reimplement Pokémon gameplay in the renderer.

The emulator/ROM remains authoritative for:
- movement and collision
- scripts and warps
- NPC behavior
- encounters
- battles
- menus and inventory
- game progression
- saves
- audio

The HD-2D code is presentation only.

## Primary working area

Prefer changes in:
- `src/voxel.rs`
- `src/voxel/`
- renderer-focused tooling/docs/tests

Avoid changing `src/cpu.rs`, `src/bus.rs`, or `src/ppu.rs` unless the task genuinely requires it. If one of those files must change, explain why before making the change.

## Preserve proven behavior

Assume existing special cases, offsets, heuristics, and measured constants solve real regressions unless tests or evidence show otherwise. Refactor cautiously.

Do not simplify away:
- actor reconstruction
- field-effect preservation
- connected-map handling
- 2D fallback detection
- verified interior profiles
- tree/edge behavior
- deterministic regression hooks

## Design priorities

Prefer generic rendering rules over map-specific hacks.

Map-specific profiles are acceptable for genuinely unique FireRed scenery, but first look for a reusable rule based on tileset, metatile pattern, dimensions, behavior, or artwork structure.

Default style priorities:
1. Preserve original FireRed artwork.
2. Add depth before replacing artwork.
3. Keep sprites pixel-sharp.
4. Use subtle lighting/shadows.
5. Favor readability over realism.
6. Avoid excessive perspective distortion.
7. Keep battle/menu fallback behavior intact.

## First vertical slice

Polish these areas before attempting all of Kanto:
- Pallet Town
- Oak's Lab
- Route 1
- Viridian City
- Viridian Pokémon Center
- Viridian Mart

This slice must demonstrate outdoor terrain, trees, tall grass, buildings, interiors, furniture, NPCs, warps, dialogue, connected maps, and fallback to 2D.

## Development sequence

Work in small, reviewable tasks:
1. renderer configuration cleanup
2. camera configuration/presets
3. actor grounding/contact shadows
4. building depth and roof treatment
5. tree presentation
6. tall grass presentation
7. water presentation
8. interior profile system
9. visual-audit automation
10. broader Kanto coverage

Do not jump to a GPU rewrite before the current scene interpretation is stable.

## Testing

After renderer changes run:

```sh
cargo fmt --check
cargo clippy --all-targets -- -D warnings
cargo test
```

When local FireRed fixtures are available, also run the existing 3D regressions and ROM layout audit documented in `docs/3d-mode.md`.

Never commit or distribute:
- ROMs
- battery saves
- save states
- extracted copyrighted game assets

The existing `.gitignore` rules for these files must remain intact.

## Before coding

For any substantial task:
1. read `docs/3d-mode.md`
2. read `docs/development.md`
3. inspect the affected renderer module
4. identify existing tests/regressions
5. make the smallest architecture change that solves the task
6. run tests
7. summarize files changed, tests run, and remaining visual risks

## Vibe-coding behavior

The user is acting primarily as art director/product owner, not as the Rust implementer. Make reasonable implementation decisions autonomously, but surface decisions that materially affect visual direction or architecture.

When visual judgment is required, prefer producing a working default plus tunable configuration rather than blocking on questions.

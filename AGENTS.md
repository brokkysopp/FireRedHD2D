# FireRed HD-2D Agent Instructions

## Project goal

Turn the existing experimental FireRed `--3d` renderer into a polished, feature-rich HD-2D / 2.5D presentation system while keeping the original ROM/emulator authoritative for gameplay.

The intended end state is not merely "FireRed with extruded tiles." It is a cinematic, highly readable diorama presentation that can render the live game in 3D, reinterpret battles and interiors spatially, support follower-style characters/Pokémon where game state permits, and provide tools for smooth cinematic camera work, replay/refilming, and keyframed shot creation.

The visual reference is a clean, soft, toy-like FireRed diorama:
- original FireRed sprite identity remains obvious
- simple geometry is used deliberately
- buildings and interiors read as physical spaces
- characters and Pokémon remain crisp sprite-based subjects unless there is a specific reason not to
- lighting and shadows are soft and readable rather than realistic
- camera composition does a large amount of the visual work
- the world should look intentionally staged, not like a raw voxel/debug extrusion
- no photorealistic/PBR-heavy treatment

## Current project state

Development has already progressed through the work represented by HD2D-001 through HD2D-005.

Treat the results of those tasks as established work:
- renderer configuration work
- camera configuration/presets
- actor grounding/contact-shadow work
- building presentation work
- tree presentation work

Do not redo or discard that work unless a regression or a clearly superior implementation requires it.

The GitHub issue state may lag behind actual implementation progress; inspect the branch and current code before assuming an issue is unfinished.

The next visual-development priorities are:
1. tall grass
2. water
3. interior generalization/polish
4. battle-space reinterpretation
5. cinematic/replay tooling
6. broader map coverage and audit automation

## Critical architecture rule

Do not reimplement Pokémon gameplay in the renderer.

The emulator/ROM remains authoritative for:
- movement and collision
- scripts and warps
- NPC behavior
- encounters
- battle mechanics
- battle state
- menus and inventory
- game progression
- save data
- audio
- map state
- actor state

The HD-2D layer may reinterpret how those systems are PRESENTED, but it must not become the authority for them.

Presentation systems may maintain cosmetic state such as:
- camera easing
- render interpolation
- cinematic shot state
- replay-camera state
- visual effect timing
- depth-of-field parameters
- render-only follower offsets
- render-only animation blending

That cosmetic state must never alter the underlying FireRed simulation.

## Primary working area

Prefer changes in:
- `src/voxel.rs`
- `src/voxel/`
- renderer-focused tooling
- renderer-focused tests
- camera/replay/editor modules added specifically for HD-2D presentation

Avoid changing:
- `src/cpu.rs`
- `src/bus.rs`
- `src/ppu.rs`

unless the task genuinely requires emulator-layer support.

If emulator-core files must change:
1. explain why renderer-side work is insufficient
2. keep the change narrowly scoped
3. prove ordinary 2D emulation still behaves correctly
4. preserve the existing accuracy tests

## Preserve proven behavior

Assume existing special cases, measured constants, offsets, and heuristics solve real regressions unless tests or direct evidence show otherwise.

Do not simplify away:
- actor reconstruction
- field-effect preservation
- connected-map handling
- 2D fallback detection
- verified interior profiles
- tree/edge behavior
- player visibility handling
- deterministic regression hooks
- battle detection/fallback logic
- current camera presets/configuration
- work already completed in HD2D-001 through HD2D-005

Refactors are allowed when they make later features easier, but preserve observable behavior first.

# Visual target

## Core appearance

The target is a polished FireRed diorama, not realistic 3D.

The rendered world should feel like:
- a miniature physical set
- clean and colorful
- soft but still pixel-art faithful
- intentionally composed
- readable during normal gameplay and when viewed from wider cinematic angles

Favor:
- low-complexity geometry
- strong silhouettes
- coherent height relationships
- soft contact shadows
- restrained directional lighting
- stable, near-orthographic or mild-perspective cameras
- subtle depth cues
- crisp sprites
- clean materials/colors derived from source artwork
- low visual noise

Avoid:
- photorealistic materials
- noisy normal maps
- excessive bloom
- excessive depth of field
- dramatic perspective distortion during ordinary play
- texture filtering that destroys pixel-art readability
- geometry detail that competes with sprites
- converting every sprite into a 3D model

## Pixel-art treatment

Original FireRed artwork remains the primary visual language.

Default rules:
- keep sprites pixel-sharp
- prefer nearest/point sampling for source pixel art
- preserve recognizable palette relationships
- avoid smoothing away sprite edges
- preserve original facade/window/door/sign art wherever possible
- add depth around the art instead of replacing it

If a 3D surface needs a material and no appropriate source texture exists, derive a simple flat/palette-matched material rather than introducing realistic textures.

## Camera language

Camera quality is a primary feature, not an afterthought.

Ordinary gameplay should generally use:
- high-angle diorama framing
- restrained perspective
- stable composition
- limited lens distortion
- clear subject visibility
- smooth following without floatiness
- configurable pitch/yaw/distance/focal behavior
- interior-specific composition where needed

The system should support distinct camera modes/presets such as:
- Legacy
- Diorama
- Interior
- Overview
- Cinematic
- Battle
- Free Camera
- Replay Camera

The camera system must be able to transition smoothly between presets.

### Smooth cinematic camera paths

Smooth cinematic camera paths are a required project capability.

Support should eventually include:
- position keyframes
- target/look-at keyframes
- FOV/focal-length keyframes
- roll where useful
- easing per segment
- Bezier/Catmull-Rom or equivalent smooth interpolation
- constant-speed or authored-speed traversal
- focus-distance / depth-of-field control where supported
- per-keyframe timing
- looping
- hold frames
- cut vs blend transitions
- follow-target shots
- orbit shots
- crane/pullback shots
- wide establishing shots
- close character shots
- battle-specific shot presets

Cinematic camera motion must remain presentation-only and must not affect game simulation timing unless a deliberate replay tool explicitly controls playback speed.

# World presentation

## Buildings

Buildings should read as designed structures, not stacks of extruded blocked cells.

Priorities:
- consistent roof planes
- clean roof silhouettes
- coherent ridge/eave treatment
- preserved FireRed facade art
- readable doors/windows/signage
- reasonable side/rear surfaces
- consistent height families
- no severe shearing near screen edges
- simple geometry over intricate geometry

For large city buildings, prefer clean block massing that matches the reference style:
- broad vertical walls
- readable facade strips
- flatter roofs where appropriate
- restrained roof thickness
- clear building separation

## Trees

Tree presentation should remain FireRed-art-first.

Prefer:
- layered billboards
- sliced/parallax tree layers
- simplified canopy depth
- clear trunk grounding
- consistent repeated silhouettes for forests

Dense tree fields should read as a cohesive forest while still preserving individual tree rhythm.

Do not worsen:
- Route 1 visibility regressions
- player visibility behind crowns
- tree-edge continuity
- connected-map tree behavior

Fully modeled trees may remain available as an alternate style, but they are not the primary reference target.

## Tall grass

Tall grass must become visibly volumetric without changing gameplay.

Desired presentation:
- original grass tile remains the base
- add clustered upright cards/blades/layers
- preserve the original palette
- use restrained motion, if any
- avoid dense geometry that hurts readability
- keep the player/Pokémon readable inside grass
- preserve FireRed's original field-effect masking over actor legs

FireRed remains authoritative for:
- encounter checks
- grass state
- movement
- field effects

## Water

Water should look more dimensional while remaining recognizably FireRed.

Desired treatment:
- source water art remains visible
- modest depth offset below shore
- subtle UV/texture animation
- restrained highlight/reflection treatment
- optional slight vertex/surface motion if a future GPU renderer supports it
- clean shoreline lips
- no realistic transparent ocean shader

## Shadows

Use shadows primarily to ground subjects.

Prefer:
- soft contact shadows
- simple directional shadows
- consistent shadow direction
- restrained opacity
- stable results while moving

Sprites should never appear to float.

# Characters and follower presentation

Characters remain billboard/pixel sprites by default.

Requirements:
- stable feet pivot
- correct depth sorting
- consistent scale
- contact shadows
- readable occlusion
- preserved field effects
- no camera-facing wobble
- cosmetic interpolation may smooth motion but may not change authoritative positions

If a follower Pokémon/companion can be inferred or exposed from the current game/mod state, support it as a render subject using the same grounding/depth rules.

Follower presentation should support:
- standing beside/behind player
- smooth render-only following
- correct sprite direction/animation where data permits
- contact shadow
- grass/occlusion interaction
- cinematic framing

Do not invent follower gameplay state when the ROM/mod does not provide it.

# Interiors

Interiors are a major target, not a secondary fallback.

The desired style is a clean cutaway/boxed-room diorama:
- raised walls
- open ceiling
- black/neutral void outside the playable room where appropriate
- clear room boundaries
- simple extruded furniture
- crisp sprite characters
- readable counters, shelves, desks, machines, planters, terminals, etc.

Interior geometry should be generalized through reusable profiles wherever practical.

Profiles should be able to describe:
- tileset/pattern identity
- width/height in metatiles
- object class
- world height
- depth
- front/top/side treatment
- whether original top-layer artwork is preserved
- collision-independent render footprint
- grouping rules for multi-tile furniture

Preserve the verified Oak's Lab and Viridian interior behavior while generalizing.

# Battle presentation

Battles are part of the HD-2D target.

Do not treat the existing flat-2D battle fallback as the permanent end state.

The final renderer should be capable of presenting battles as spatial diorama scenes while leaving FireRed's battle simulation authoritative.

## Battle architecture goals

The battle presentation layer should eventually:
- detect battle state reliably
- identify player-side and foe-side battlers
- reconstruct or capture relevant battler sprites
- place battlers in 3D world positions
- preserve original battle UI/text boxes
- preserve HP bars and battle menus unless intentionally restyled later
- map original battle animation elements into a spatial scene where practical
- keep compatibility fallback to original 2D for unsupported effects

## Battle visual target

Use:
- simple 3D battlefield ground
- stylized route/environment backdrop
- billboard Pokémon sprites
- soft contact shadows
- preserved original sprite art
- readable battle HUD
- camera framing that makes the scene feel spatial without harming battle clarity

Wild battles may use environment context inferred from current map/terrain where practical.

Trainer battles may use a stylized arena based on current location/category.

## Battle animation remapping

Original battle animations should be spatially remapped where feasible.

Aim to support:
- source/target world anchors
- projectile trajectories
- impact effects
- screen effects translated into local/world effects when sensible
- sprite transforms
- hit flashes
- shake
- particles
- temporary props/effects

Do not require every animation to be fully remapped before battles become usable.

Use graceful fallback:
1. spatial remap when supported
2. hybrid overlay when partially supported
3. original flat rendering when unsupported

# Replay, refilm, and cinematic tooling

A replay/refilm system is an intended core feature.

The desired workflow is:
1. play the game normally
2. capture enough deterministic state/input/frame information to reproduce a moment
3. replay that sequence
4. detach or override the presentation camera
5. scrub through the sequence
6. add camera keyframes
7. change camera speed/FOV/target
8. render or record the sequence from new angles

The system should be designed so gameplay simulation remains deterministic and camera work is layered on top.

## Replay requirements

Long-term support should include:
- record inputs by frame
- record/load deterministic starting state
- frame-accurate playback
- pause
- frame step
- seek/scrub where architecture permits
- playback speed control
- slow motion
- fast forward
- camera-independent replay
- deterministic repeated output where possible
- exportable replay metadata that does not contain ROM data

Reuse existing deterministic/headless input tooling where practical instead of inventing a completely separate replay system.

# Timeline/keyframe editor

A timeline/keyframe editor is part of the desired toolset.

It does not need to be a full professional NLE, but it should eventually support:

- play/pause
- frame/time scrubber
- current frame/time display
- playback speed
- keyframe markers
- camera position keyframes
- camera target keyframes
- FOV/focal keyframes
- interpolation/easing choice
- shot cuts
- shot ranges
- delete/move keyframes
- duplicate keyframes
- loop range
- follow target selection
- free camera toggle
- save/load camera tracks

The editor may be implemented as:
- native in-app UI
- a development-only desktop overlay
- an external local control panel
- or another practical interface

Choose the architecture that gives the fastest reliable iteration without destabilizing emulation.

# Live visual harness

A live visual-control/debug harness is desirable.

It should eventually expose useful runtime tuning for:
- camera
- lighting
- wall/building heights
- tree treatment
- grass density
- water settings
- depth/tilt effects
- shadow strength
- environment style
- replay state
- camera tracks

The point is to make the renderer art-directable without recompiling for every visual adjustment.

Prefer a compact developer UI over dozens of undocumented environment variables as the system matures.

# Wide/cinematic map views

The renderer should support views substantially wider than the original 240x160 GBA viewport.

This includes:
- town establishing shots
- route establishing shots
- forest-wide shots
- city overview shots
- large interior views
- battle establishing shots

Requirements:
- decode/render enough surrounding map data for the requested camera
- preserve connected-map terrain
- avoid exposing uninitialized/invalid map regions
- keep actor visibility/loading limitations explicit
- do not fake gameplay state outside what FireRed actually has loaded unless rendering static map scenery from ROM data

Wide rendering is a presentation feature; it does not expand the actual gameplay simulation area.

# Runtime visual priorities

When deciding what to polish next, prioritize in this order:

1. player/subject readability
2. camera composition
3. coherent geometry
4. sprite grounding
5. source-art preservation
6. soft lighting/shadows
7. environment depth
8. visual effects
9. cinematic flourishes

A technically sophisticated effect that harms readability should be removed or reduced.

# Generic rules vs special cases

Prefer generic rules over per-map hacks.

Before adding a map-specific condition, ask whether the problem can be solved by:
- tileset identity
- metatile pattern
- neighboring tiles
- dimensions
- collision/behavior metadata
- art-layer structure
- map category
- interior profile
- reusable scenery family

Map-specific profiles are acceptable for genuinely unique locations or assets.

Document unavoidable special cases.

# Renderer backend strategy

Do not perform a GPU rewrite merely because it is more modern.

Continue improving the existing renderer while scene interpretation, geometry classification, and behavior are still evolving.

A GPU backend such as `wgpu` is explicitly allowed and likely useful when it materially enables:
- larger render resolution
- shader-based water/grass
- shadow maps
- better post processing
- depth-of-field
- higher scene complexity
- cinematic rendering
- better performance
- flexible offscreen rendering

If/when introducing a GPU renderer:
- preserve the semantic/world reconstruction layer
- do not tie FireRed memory decoding directly to GPU APIs
- keep a clean separation between world interpretation and rendering backend
- retain a fallback/reference path where useful for regressions

# First polished content target

The original first vertical slice remains an important regression/reference set:
- Pallet Town
- Oak's Lab
- Route 1
- Viridian City
- Viridian Pokémon Center
- Viridian Mart

These locations should remain polished as work expands.

Next content targets should include:
- Viridian Forest
- Pewter City
- Pewter Gym
- first major battle flows
- representative caves
- representative large cities
- representative special interiors

Do not expand map coverage at the expense of breaking the polished reference areas.

# Testing

After renderer changes run:

```sh
cargo fmt --check
cargo clippy --all-targets -- -D warnings
cargo test
```

When local FireRed fixtures are available, also run the existing 3D regressions and ROM layout audit documented in `docs/3d-mode.md`.

For camera/replay/battle work, add deterministic tests where practical.

For visual changes:
- capture before/after screenshots
- preserve representative baseline shots
- inspect player grounding
- inspect edge cases
- inspect warps/transitions
- inspect battle transitions
- inspect interiors
- inspect wide-camera bounds

Never commit or distribute:
- ROMs
- battery saves
- save states containing copyrighted ROM data
- extracted copyrighted game assets

The existing `.gitignore` protections must remain intact.

# Before coding

For any substantial task:

1. Read `AGENTS.md`.
2. Read `docs/3d-mode.md`.
3. Read `docs/development.md`.
4. Read the relevant roadmap/backlog/issue.
5. Inspect the current implementation on `hd2d-development`; do not assume old issue text reflects current code.
6. Identify existing regression tests.
7. State a short implementation plan.
8. Make the implementation rather than stopping at analysis.
9. Run formatting/lint/tests.
10. Summarize:
   - files changed
   - architecture decisions
   - behavior preserved
   - tests run/results
   - visual risks
   - next recommended task

# Vibe-coding behavior

The user is acting primarily as art director/product owner rather than as the Rust implementer.

Make reasonable implementation choices autonomously.

Do not block on minor implementation decisions that can be made safely.

When several approaches are viable:
- choose the one that best preserves emulator correctness
- favors reusable systems
- makes visual tuning easy
- moves toward the target presentation
- keeps the project testable

Surface decisions to the user when they materially affect:
- art direction
- architecture
- compatibility
- performance
- scope

When visual judgment is required, prefer:
- a strong working default
- exposed tuning controls
- comparison presets/screenshots

rather than asking the user to choose numerical implementation details before anything is visible.

# Definition of success

The project succeeds when FireRed can be played normally while being presented as a polished 3D/HD-2D diorama, and the same underlying gameplay can be re-presented cinematically through controllable cameras, replay/refilm tools, spatial battle scenes, and a timeline/keyframe workflow without sacrificing the reliability of the underlying emulator.

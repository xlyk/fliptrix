# fliptrix

Single-file Three.js app (`tricks-3d.html`, ES module + importmap, no build step). Test by serving locally (`python3 -m http.server`) and screenshotting deterministic poses via URL params (`?trick=tre&t=0.45&cam=top`).

## Invariants — break these and tricks stop reading correctly

- **`flip_trick_foot_placement_v3_portable.html` is the audited source of truth** for foot placements (`ff`/`bf`), pressure weights (`bp`/`fp`), and scoop/flick directions. Never edit it. The 3D `TRICKS` object ports it; `svgToLocal` maps SVG viewBox coords → deck-local meters.
- **Pure-in-t**: `boardPose`, `footPose`, `riderPose`, `leanAt`, `bodyYawAt`, `flickContactT` are deterministic functions of `t` (no state, no Date/random). Scrubbing, trails (which re-sample `footPose`), and resume all depend on this.
- **Spin chirality**: positive `spinDeg` = backside = tail sweeps heel-ward (−X) = **+Y world rotation**. The original spec said "BS = CW from above" but that contradicts the audited scoop arrows in a right-handed frame (+Z nose, +X toe); the scoop direction wins. Kickflip-positive flip = `AxisAngle(+Z, +θ)` (toe edge rises first).
- **Rotations are impulse-shaped** (`easeOut`): spin starts at launch (t=0.20, the scoop), flip starts at `flickContactT(tr)` — spin-dependent (~0.23 for 360s, 0.28 flip-only) because the nose meets the foot after ~35° of yaw. Both complete by `PH.catch` (0.78).
- **The scoop is not authored**: the back foot rides the rotating toe pocket (`ghost()` through launch), releases tangentially, recoils. Its path correctness comes from `boardPose`.
- **Stance/nollie are render-time reflections** in `setPose` (stance: negate `pos.x`, quat y/z; nollie: z-mirror about `rollZ`, negate quat x/y + feet `scale.z = -1` because reflected poses need reflected geometry). Pose math never changes; child rotations (truck steer, pelvis yaw) flip sign per mirror.
- **Deck geometry and foot contact share one profile**: `deckWidthAt`/`deckLiftAt` feed both the mesh and `onDeck`/`svgToLocal`. The shoe sole and upper share `halfW`/`topH`. Change a profile constant → both sides move together.

## Workflows

- **Tuning**: per-trick sliders in the tune panel mutate `TRICKS[k]` live (persisted in localStorage). "copy json" exports non-default diffs — bake them into the trick's entry in `TRICKS`; materialization (`?? defaults`) makes baked values the new reset point.
- **Verification**: headless Chrome `--headless=new --use-angle=metal --virtual-time-budget=8000 --screenshot=...` against the local server; the page shows a red error banner on any JS error (visible in screenshots).
- **Provenance**: `shove360`/`bigspin` cue text is authored (not from the 2D file) — noted in a comment at their entries. Heel-family front-foot yaw was deliberately rotated from the audited 10° to ~28° for 3D readability.

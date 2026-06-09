# fliptrix

interactive 3d skateboard flip-trick visualizer. open `tricks-3d.html` in chrome or safari — no build step, only network dependency is the three.js cdn.

- 12 tricks (ollie through laser flip, 360 shove-it, bigspin) with animated board, ghost feet/legs, pressure points, and per-trick tuning knobs
- `flip_trick_foot_placement_v3_portable.html` is the original 2d teaching widget — its foot placements and scoop/flick directions are the audited source of truth (do not edit)
- url params make any state shareable: `?trick=tre&t=0.45&cam=top&nollie=1`

see CLAUDE.md for the invariants that keep the animation correct.

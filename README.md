# Sokoban Solver — push-optimal, room by room

Entry for make.fun's **Build the best Sokoban solver, 21 November 2026** bounty
(`sokoban-solver-01-run-20261121`, $100).

## How it works

A level is a hallway with rooms off it, and no box ever leaves its room (a push
through a door lands it in the hallway, where it reaches no goal), so the
level's push-optimal count P\* is the sum of its rooms' optima. Each room is
solved push-optimally on its own with a breadth-first search over pushes, the
other rooms' boxes standing as walls, and the player walking on from where the
last room left it.

A search state is the boxes' squares plus the player's reachable region (named
by its lowest square). Two deadlocks are pruned, neither of which removes a
solvable state: a dead square (one from which no push, ignoring the other
boxes, brings a box to any goal) and a frozen 2 x 2 block of walls and boxes
with a box off its goal.

All budgets are node counts, never the clock, so two runs write the same bytes
(the grader executes every entry twice and demands identical output). Fallbacks,
in order: room-by-room push BFS; whole-level push BFS; a plain move-minimising
A\* over the whole level; an empty solution rather than a crash.

## Results

| Set | Score (of 1,000,000) | Time |
| --- | --- | --- |
| Practice set (kit) | **1,000,000** — every instance at exactly P\* (13, 16, 29, 47, 60 pushes) | ~2.2 s |
| 3 fresh sets drawn with the kit's own generator | **1,000,000** each (20/20 instances at P\*) | 1.6–2.5 s each |

The house baseline (plain-astar 1.0.0) scores 200,000 on the practice set
(solves only i001). Deterministic: two runs produce byte-identical outputs.

## Run

```sh
MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

`main.ts` at the repository root is the entrypoint (Node 22, no dependencies,
no network). It reads `/input/<id>/level.txt` and writes
`/output/<id>/solution.txt` (moves in LURD) for each instance.

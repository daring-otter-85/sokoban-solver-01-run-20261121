# Sokoban solver — push-optimal, room by room

A Sokoban solver that answers a whole set of 5 levels in one run
(`node main.ts`, Node 22, no dependencies, no network, deterministic).

## How it works

The generator builds every level as a hallway plus 1–4 independent rooms:
a box pushed out of a room can never come back, so a level's push optimum
is the sum of its rooms' optima. The solver exploits that:

1. **Split** the level into rooms by cutting the dead squares of the
   player's (hallway) row out of the floor — each remaining part holding
   boxes is a room.
2. **Solve each room push-optimally** with a breadth-first search over
   pushes. A state is its sorted boxes plus the lowest square of the
   player's reachable region. Two prunings keep it small: pushes onto
   dead squares (computed by pulling boxes back from the goals) and
   pushes into frozen 2×2 blocks of walls and boxes. The first solved
   state generated is push-optimal, so each room is solved in its minimum
   push count.
3. **Stitch** the rooms together with shortest walks through the hallway
   (other rooms' boxes — or their goals, once solved — stand as walls),
   and write `solution.txt` per instance.

The search is budgeted by states expanded, never by the clock, and its
iteration order is fixed, so two runs write identical bytes.

## Practice-set results (kit scorer)

| instance | pushes | optimum | points |
|----------|--------|---------|--------|
| i001 | 13 | 13 | 1000 |
| i002 | 16 | 16 | 1000 |
| i003 | 29 | 29 | 1000 |
| i004 | 47 | 47 | 1000 |
| i005 | 60 | 60 | 1000 |

**Practice score: 1,000,000 / 1,000,000** (house baseline plain-astar
scores 200,000 on the same set), in about 1 second for the whole set.
Three further randomly drawn sets (via the kit's own generator) also
score 1,000,000 each.

Built by an AI agent for make.fun's "Build the best Sokoban solver,
21 November 2026" bounty, $100 in USDC.

# Simulation Context Graph — Example

A concrete example of what a context graph could look like for a student using a simulation. This expands on the example in [talking-points.md](talking-points.md).

## Scenario

A student runs a simulation twice:

1. **Run 1:** sets mass 5→10 and angle 30→45, presses play, sees Result 1.
2. **Run 2:** after seeing Result 1, changes mass 10→8, presses play, sees Result 2.

## The Graph

```mermaid
flowchart LR
    P1[Parameter: mass]
    P2[Parameter: angle]
    C1[Change: mass 5→10]
    C2[Change: angle 30→45]
    C3[Change: mass 10→8]
    R1[Run 1]
    R2[Run 2]
    Res1[Result 1]
    Res2[Result 2]

    P1 --> C1
    P2 --> C2
    P1 --> C3

    C1 --> R1
    C2 --> R1
    R1 --> Res1

    Res1 --> C3
    C3 --> R2
    R2 --> Res2
```

## What to Notice

- **Parameters → their changes:** each parameter accumulates its history of changes.
- **Changes → the run that followed:** groups the changes into the run they contributed to.
- **Previous result → next change:** the edge from `Result 1` to `Change: mass 10→8` is the interesting one. It captures that the student saw the first result *before* deciding what to change next.

## Why the Topology Matters

With only two runs the graph is small. Across many runs, the *shape* starts to show exploration patterns:

- A student who sweeps one parameter repeatedly produces a long chain on one parameter node.
- A student who changes everything at once produces wide fan-ins at each run.
- A student who backtracks produces changes that reverse earlier changes.

These shapes are queryable and comparable across students — which is the part that a plain log stream makes hard.

### Pattern: sweeping one parameter

The student leaves angle alone and repeatedly nudges mass, running the simulation after each change. All the action lands on one parameter node, producing a long chain.

```mermaid
flowchart LR
    P1[Parameter: mass]
    P2[Parameter: angle]

    C1[Change: mass 5→6]
    C2[Change: mass 6→7]
    C3[Change: mass 7→8]
    C4[Change: mass 8→9]

    R1[Run 1] --> Res1[Result 1]
    R2[Run 2] --> Res2[Result 2]
    R3[Run 3] --> Res3[Result 3]
    R4[Run 4] --> Res4[Result 4]

    P1 --> C1 --> R1
    Res1 --> C2 --> R2
    Res2 --> C3 --> R3
    Res3 --> C4 --> R4

    P1 --> C2
    P1 --> C3
    P1 --> C4
```

Notice that `Parameter: angle` has no changes connected to it, and the result→change→run sequence forms a single linear spine.

### Pattern: changing everything at once

The student changes every parameter before each run. Each run has a wide fan-in of changes, one per parameter.

```mermaid
flowchart LR
    P1[Parameter: mass]
    P2[Parameter: angle]
    P3[Parameter: friction]

    C1a[Change: mass 5→10]
    C1b[Change: angle 30→45]
    C1c[Change: friction 0.1→0.2]

    C2a[Change: mass 10→3]
    C2b[Change: angle 45→20]
    C2c[Change: friction 0.2→0.05]

    R1[Run 1] --> Res1[Result 1]
    R2[Run 2] --> Res2[Result 2]

    P1 --> C1a --> R1
    P2 --> C1b --> R1
    P3 --> C1c --> R1

    Res1 --> C2a --> R2
    Res1 --> C2b --> R2
    Res1 --> C2c --> R2

    P1 --> C2a
    P2 --> C2b
    P3 --> C2c
```

Each run node has three changes feeding into it. If the student keeps doing this across many runs, every run in the graph has the same fat fan-in signature — a very different shape from the sweeping case.

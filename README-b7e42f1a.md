# Sentinel Reflections Puzzle - `b7e42f1a`

A custom ARC-AGI puzzle combining 4 interacting rules: Object Detection, Point Reflection, Conditional Shape Shifting, and a hidden Distance Gate threshold.

---

## Overview

You are given an 11x11 black grid containing:

- One "Sentinel" object: a plus/cross shape made of 5 pixels (4 red arms + 1 colored center).
- One or more "Beacon" pixels: isolated single pixels of color 8 (azure).

The transformation replaces each beacon with a colored bloom shape and (sometimes) creates a blue ghost shape at the reflected position on the opposite side of the sentinel.

The trick: not every beacon gets a ghost. Only beacons that are FAR from the sentinel produce ghosts. Close beacons produce blooms only.

---

## Color Palette

| Code | Color   | Role                                |
|------|---------|-------------------------------------|
| 0    | Black   | Empty / Background                  |
| 1    | Blue    | Ghost shape color                   |
| 2    | Red     | Sentinel arm color (frame)          |
| 3    | Green   | Gem color (varies per example)      |
| 4    | Yellow  | Gem color (varies per example)      |
| 6    | Magenta | Gem color (varies per example)      |
| 7    | Orange  | Gem color (varies per example)      |
| 8    | Azure   | Beacon pixel (input only)           |

---

## Objects in the Input

### The Sentinel

A plus-shaped object made of 5 pixels:

```
    . 2 .
    2 G 2
    . 2 .
```

- The 4 outer pixels are always color 2 (red). These are the "arms."
- The center pixel G is the "gem color." It changes per example (3, 4, 6, or 7).
- The sentinel center position is the reference point for all rules.

### Beacons

Single isolated pixels of color 8 (azure), placed at various locations on the grid.

---

## The Four Rules

### Rule 1 -- Sentinel Preservation

The sentinel stays exactly as it is. Its 5 pixels (4 red arms + 1 gem center) are never modified.

### Rule 2 -- Beacon Bloom

Each beacon pixel (8) is removed and replaced by a small shape painted in the gem color G.

The shape depends on alignment:

| Beacon Alignment          | Bloom Shape                          |
|---------------------------|--------------------------------------|
| Off-axis (not aligned)    | Plus/cross (5 cells: center + NSEW)  |
| Same row as sentinel      | Vertical bar (3 cells: center + N/S) |

Visual examples:

```
Off-axis bloom:         Same-row bloom:

      . G .                   G
      G G G                   G
      . G .                   G
```

### Rule 3 -- Ghost Reflection

For each beacon, compute its point reflection through the sentinel center. The ghost appears at this reflected position.

Point reflection formula:

```
ghost_row = 2 * sentinel_row - beacon_row
ghost_col = 2 * sentinel_col - beacon_col
```

In simple terms: the ghost sits on the exact opposite side of the sentinel, at the same distance.

The ghost shape is painted in color 1 (blue), and its orientation is swapped:

| Beacon Bloom Shape | Ghost Shape                            |
|--------------------|----------------------------------------|
| Plus/cross         | Plus/cross                             |
| Vertical bar       | Horizontal bar (3 cells: center + W/E) |

Visual examples:

```
Off-axis ghost:         Same-row ghost (swapped orientation):

      . 1 .
      1 1 1                 1 1 1
      . 1 .
```

IMPORTANT: Ghost pixels only fill empty (0) cells. They never overwrite the sentinel or any bloom.

### Rule 4 -- Distance Gate (THE TRICKY RULE)

Not every beacon gets a ghost. There is a hidden distance threshold.

For each beacon, calculate its Manhattan distance from the sentinel center:

```
distance = |beacon_row - sentinel_row| + |beacon_col - sentinel_col|
```

Then apply the gate:

| Distance    | What Happens                       |
|-------------|------------------------------------|
| 5 or less   | Bloom ONLY. Ghost is NOT created.  |
| More than 5 | Bloom AND Ghost are both created.  |

This is the hardest rule to discover because it requires counting cells and noticing that some beacons produce ghosts while others do not.

---

## Training Examples

### Train Example 1 -- One FAR beacon, off-axis

**Input:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   .   .
R1  |  .   .   .   .   .   .   .   .   .   .   .
R2  |  .   .   .   .   .   .   .   .   .   8   .
R3  |  .   .   .   .   .   .   .   .   .   .   .
R4  |  .   .   .   .   .   2   .   .   .   .   .
R5  |  .   .   .   .   2   3   2   .   .   .   .
R6  |  .   .   .   .   .   2   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   .   .   .   .   .   .   .   .   .
R9  |  .   .   .   .   .   .   .   .   .   .   .
R10 |  .   .   .   .   .   .   .   .   .   .   .
```

**Identified objects:**

- Sentinel center: (R5, C5), gem color = 3 (green)
- Beacon at (R2, C9)

**Step-by-step:**

1. Alignment check: Row 2 != Row 5, Col 9 != Col 5. Off-axis.
2. Distance: |5-2| + |5-9| = 3 + 4 = 7. This is more than 5, so FAR. Ghost WILL appear.
3. Bloom: Plus/cross of green (3) at (R2, C9). Fills (R1,C9), (R2,C8), (R2,C9), (R2,C10), (R3,C9).
4. Ghost position: (2*5-2, 2*5-9) = (R8, C1). Plus/cross of blue (1). Fills (R7,C1), (R8,C0), (R8,C1), (R8,C2), (R9,C1).

**Output:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   .   .
R1  |  .   .   .   .   .   .   .   .   .   3   .
R2  |  .   .   .   .   .   .   .   .   3   3   3
R3  |  .   .   .   .   .   .   .   .   .   3   .
R4  |  .   .   .   .   .   2   .   .   .   .   .
R5  |  .   .   .   .   2   3   2   .   .   .   .
R6  |  .   .   .   .   .   2   .   .   .   .   .
R7  |  .   1   .   .   .   .   .   .   .   .   .
R8  |  1   1   1   .   .   .   .   .   .   .   .
R9  |  .   1   .   .   .   .   .   .   .   .   .
R10 |  .   .   .   .   .   .   .   .   .   .   .
```

**What this example teaches:**

- Rule 1: The sentinel is preserved.
- Rule 2: The beacon becomes a green cross (gem color).
- Rule 3: A blue cross ghost appears on the opposite side of the sentinel.
- The beacon is far enough (distance 7) so the ghost appears. But at this point the solver does not know about the distance threshold yet.

---

### Train Example 2 -- One CLOSE beacon, one FAR beacon (Distance Gate revealed)

**Input:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   .   .
R1  |  .   .   .   .   .   .   .   .   .   .   .
R2  |  .   .   .   .   .   .   .   .   .   .   .
R3  |  .   .   .   8   .   .   .   .   .   .   .
R4  |  .   .   .   .   .   2   .   .   .   .   .
R5  |  .   .   .   .   2   6   2   .   .   .   .
R6  |  .   .   .   .   .   2   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   .   .   .   .   .   .   .   .   .
R9  |  .   .   8   .   .   .   .   .   .   .   .
R10 |  .   .   .   .   .   .   .   .   .   .   .
```

**Identified objects:**

- Sentinel center: (R5, C5), gem color = 6 (magenta)
- Beacon A at (R3, C3)
- Beacon B at (R9, C2)

**Step-by-step for Beacon A at (R3, C3):**

1. Alignment: Row 3 != Row 5, Col 3 != Col 5. Off-axis.
2. Distance: |5-3| + |5-3| = 2 + 2 = 4. This is 5 or less, so CLOSE. Ghost will NOT appear.
3. Bloom: Plus/cross of magenta (6) at (R3, C3). Fills (R2,C3), (R3,C2), (R3,C3), (R3,C4), (R4,C3).
4. Ghost: SUPPRESSED. Nothing placed.

**Step-by-step for Beacon B at (R9, C2):**

1. Alignment: Row 9 != Row 5, Col 2 != Col 5. Off-axis.
2. Distance: |5-9| + |5-2| = 4 + 3 = 7. This is more than 5, so FAR. Ghost WILL appear.
3. Bloom: Plus/cross of magenta (6) at (R9, C2). Fills (R8,C2), (R9,C1), (R9,C2), (R9,C3), (R10,C2).
4. Ghost position: (2*5-9, 2*5-2) = (R1, C8). Plus/cross of blue (1). Fills (R0,C8), (R1,C7), (R1,C8), (R1,C9), (R2,C8).

**Output:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   1   .   .
R1  |  .   .   .   .   .   .   .   1   1   1   .
R2  |  .   .   .   6   .   .   .   .   1   .   .
R3  |  .   .   6   6   6   .   .   .   .   .   .
R4  |  .   .   .   6   .   2   .   .   .   .   .
R5  |  .   .   .   .   2   6   2   .   .   .   .
R6  |  .   .   .   .   .   2   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   6   .   .   .   .   .   .   .   .
R9  |  .   6   6   6   .   .   .   .   .   .   .
R10 |  .   .   6   .   .   .   .   .   .   .   .
```

**What this example teaches:**

This is the KEY example. The solver sees two beacons that are both off-axis, both get magenta bloom crosses -- but only one of them (the bottom one) has a blue ghost cross on the opposite side. The top beacon has NO ghost at all.

The solver must ask: "Why does one beacon produce a ghost and the other does not?"

The answer is the distance gate. Beacon A is 4 steps away (close), Beacon B is 7 steps away (far). The threshold is 5.

---

### Train Example 3 -- CLOSE same-row beacon + FAR off-axis beacon

**Input:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   .   .
R1  |  .   .   .   .   .   .   .   .   .   8   .
R2  |  .   .   .   .   .   .   .   .   .   .   .
R3  |  .   .   .   .   .   .   .   .   .   .   .
R4  |  .   .   .   .   .   2   .   .   .   .   .
R5  |  .   8   .   .   2   7   2   .   .   .   .
R6  |  .   .   .   .   .   2   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   .   .   .   .   .   .   .   .   .
R9  |  .   .   .   .   .   .   .   .   .   .   .
R10 |  .   .   .   .   .   .   .   .   .   .   .
```

**Identified objects:**

- Sentinel center: (R5, C5), gem color = 7 (orange)
- Beacon A at (R5, C1) -- note: same row as sentinel!
- Beacon B at (R1, C9)

**Step-by-step for Beacon A at (R5, C1):**

1. Alignment: Row 5 == Row 5. SAME ROW!
2. Distance: |5-5| + |5-1| = 0 + 4 = 4. This is 5 or less, so CLOSE. Ghost will NOT appear.
3. Bloom: Because same-row, bloom is a VERTICAL BAR (not a cross). Three cells of orange (7) at (R4,C1), (R5,C1), (R6,C1).
4. Ghost: SUPPRESSED. Nothing placed.

**Step-by-step for Beacon B at (R1, C9):**

1. Alignment: Row 1 != Row 5, Col 9 != Col 5. Off-axis.
2. Distance: |5-1| + |5-9| = 4 + 4 = 8. This is more than 5, so FAR. Ghost WILL appear.
3. Bloom: Plus/cross of orange (7) at (R1, C9). Fills (R0,C9), (R1,C8), (R1,C9), (R1,C10), (R2,C9).
4. Ghost position: (2*5-1, 2*5-9) = (R9, C1). Plus/cross of blue (1). Fills (R8,C1), (R9,C0), (R9,C1), (R9,C2), (R10,C1).

**Output:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   7   .
R1  |  .   .   .   .   .   .   .   .   7   7   7
R2  |  .   .   .   .   .   .   .   .   .   7   .
R3  |  .   .   .   .   .   .   .   .   .   .   .
R4  |  .   7   .   .   .   2   .   .   .   .   .
R5  |  .   7   .   .   2   7   2   .   .   .   .
R6  |  .   7   .   .   .   2   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   1   .   .   .   .   .   .   .   .   .
R9  |  1   1   1   .   .   .   .   .   .   .   .
R10 |  .   1   .   .   .   .   .   .   .   .   .
```

**What this example teaches:**

This example layers TWO tricky things at once:

1. The same-row conditional rule: Beacon A is on the same row as the sentinel, so its bloom becomes a vertical bar instead of a cross. Compare the vertical bar at column 1 (rows 4-5-6) with the normal cross at column 9 (rows 0-1-2).

2. The distance gate again: Beacon A is close (distance 4) so no ghost appears. Beacon B is far (distance 8) so a blue ghost cross appears at (R9, C1).

Notice how the ghost of Beacon B sits at (R9, C1) and Beacon A's bloom sits at column 1 as well -- but they do not overlap because they are in different rows (R4-R6 vs R8-R10).

---

## Test Case

**Input:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   .   .   .   .   .   .   .   .   .   .
R1  |  .   .   .   .   .   .   .   .   .   .   .
R2  |  .   .   .   .   .   .   .   .   .   .   .
R3  |  .   .   .   8   .   .   .   .   .   .   .
R4  |  .   .   .   .   2   .   .   .   .   .   .
R5  |  .   .   .   2   3   2   .   .   8   .   .
R6  |  .   .   .   .   2   .   .   .   .   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   .   .   .   .   .   .   .   .   .
R9  |  .   .   .   .   .   .   .   8   .   .   .
R10 |  .   .   .   .   .   .   .   .   .   .   .
```

**Objects:**

- Sentinel center: (R5, C4), gem color = 3 (green)
- Beacon A at (R3, C3)
- Beacon B at (R5, C8)
- Beacon C at (R9, C7)

**Beacon analysis:**

| Beacon | Position | Alignment | Distance           | Gate  | Bloom          | Ghost          |
|--------|----------|-----------|--------------------|-------|----------------|----------------|
| A      | (R3,C3) | Off-axis  | |5-3|+|4-3| = 2+1 = 3 | CLOSE | Cross at (R3,C3)  | NONE      |
| B      | (R5,C8) | Same row  | |5-5|+|4-8| = 0+4 = 4 | CLOSE | Vertical bar at (R5,C8) | NONE |
| C      | (R9,C7) | Off-axis  | |5-9|+|4-7| = 4+3 = 7 | FAR   | Cross at (R9,C7) | Cross at (R1,C1) |

**Expected output:**

```
      C0  C1  C2  C3  C4  C5  C6  C7  C8  C9  C10
R0  |  .   1   .   .   .   .   .   .   .   .   .
R1  |  1   1   1   .   .   .   .   .   .   .   .
R2  |  .   1   .   3   .   .   .   .   .   .   .
R3  |  .   .   3   3   3   .   .   .   .   .   .
R4  |  .   .   .   3   2   .   .   .   3   .   .
R5  |  .   .   .   2   3   2   .   .   3   .   .
R6  |  .   .   .   .   2   .   .   .   3   .   .
R7  |  .   .   .   .   .   .   .   .   .   .   .
R8  |  .   .   .   .   .   .   .   3   .   .   .
R9  |  .   .   .   .   .   .   3   3   3   .   .
R10 |  .   .   .   .   .   .   .   3   .   .   .
```

---

## What Makes This Hard

| Challenge | Why It Is Difficult |
|-----------|---------------------|
| 4 simultaneous rules | Solver must discover sentinel preservation, bloom color/shape, ghost reflection, AND the distance gate all at once |
| Hidden distance threshold | The distance gate is never visually obvious. You have to count Manhattan distance and notice the cutoff at 5 |
| Two types of absence | A ghost can be missing because (a) the reflected position is off-grid, or (b) the beacon is too close. The solver must distinguish these |
| Conditional shape shift | Same-row beacons produce vertical bars (not crosses), and their ghosts become horizontal bars. This interacts with the distance gate |
| Multiple beacons | Examples 2 and 3 have two beacons each, where one gets a ghost and the other does not. The solver must figure out what differentiates them |
| Gem color changes | The gem color is different in every example (3, 6, 7, 3). The solver must identify it from the sentinel center each time |
| 3 training examples | Only 3 input-output pairs to figure out all 4 rules |

---

## Rule Summary Table

| Rule | Description | Applies When |
|------|-------------|--------------|
| Rule 1 | Sentinel preserved unchanged | Always |
| Rule 2 | Beacon replaced by bloom in gem color | Always |
| Rule 3 | Blue ghost cross at reflected position | Only if distance > 5 (Rule 4) |
| Rule 4 | Distance gate: Manhattan distance <= 5 suppresses ghost | Always checked before creating ghost |

### Bloom shape lookup:

| Beacon Alignment | Bloom Shape | Ghost Shape (if created) |
|------------------|-------------|--------------------------|
| Off-axis         | Plus/cross  | Plus/cross               |
| Same row         | Vertical bar | Horizontal bar          |

### Distance gate lookup:

| Manhattan Distance | Bloom | Ghost |
|--------------------|-------|-------|
| 5 or less (CLOSE)  | YES   | NO    |
| More than 5 (FAR)  | YES   | YES   |

---

## Puzzle Categories Used

This puzzle combines five cognitive ability categories from ARC-AGI:

1. **Object Detection** -- Identify the sentinel (plus shape with colored center) and beacon pixels (isolated azure dots) in a sparse 11x11 grid.

2. **Geometric Transformations** -- Point reflection through the sentinel center to compute ghost positions.

3. **Pattern Recognition** -- Recognize that blooms and ghosts are plus-shaped (or bar-shaped), and that the sentinel structure is consistent.

4. **Logic and Conditional Rules** -- Same-row alignment changes bloom shape from cross to vertical bar. Distance gate suppresses ghosts for close beacons.

5. **Topology / Proximity** -- Manhattan distance calculation determines whether a ghost is created or suppressed.

---

## File Location

```
ARC-AGI-2/data/prince-training/
    b7e42f1a.json              <- the puzzle file
    generate_b7e42f1a.py       <- generator and validator script
    README-b7e42f1a.md         <- this file
```

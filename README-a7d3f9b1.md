# Puzzle a7d3f9b1 — "Diamond Sentinels and Quadrant Echoes"

## Overview

An **11×11** grid is divided into **4 quadrants** by a gray (color 5) cross running along row 5 and column 5. Plus-shaped "sentinel" diamonds (a center cell + 4 cardinal neighbors) are placed in some quadrants. The transformation applies three interacting rules involving **reflection**, **conditional color mapping**, and **gap-pattern border marking**.

---

## Grid Structure

```
TL quadrant  |  TR quadrant
 (rows 0-4,  |  (rows 0-4,
  cols 0-4)  |   cols 6-10)
-------------+--------------  ← gray cross (row 5)
BL quadrant  |  BR quadrant
 (rows 6-10, |  (rows 6-10,
  cols 0-4)  |   cols 6-10)
```

The gray cross at row 5 and column 5 (color 5) **never changes** between input and output.

### Quadrant Numbering

```
  TL = 2nd quadrant  |  TR = 1st quadrant
  -------------------+--------------------
  BL = 3rd quadrant  |  BR = 4th quadrant
```

Order: **TR(1) → TL(2) → BL(3) → BR(4)**

---

## Rules

### Rule 1 — Geometric Reflection (Transformation)

Each sentinel diamond is **reflected across the gray cross axes** into every quadrant that does **not** already contain a sentinel in the input.

- To reach a **horizontally adjacent** quadrant (same row band, opposite column band): mirror the position across the **vertical axis** (column 5).
- To reach a **vertically adjacent** quadrant (same column band, opposite row band): mirror the position across the **horizontal axis** (row 5).
- To reach the **diagonally opposite** quadrant: mirror across **both** axes.

The reflection formula mirrors a position `(r, c)` by computing `(10 - r, c)` for a row flip, `(r, 10 - c)` for a column flip, or `(10 - r, 10 - c)` for both.

### Rule 2 — Conditional Color Mapping (Logic)

When a sentinel is reflected into a target quadrant, the **color of the reflected copy** depends on the spatial relationship between the source and target quadrants:

| Relationship | Reflected Color |
|---|---|
| **Diagonally opposite** (e.g., TL → BR) | **Same** as the original sentinel's color |
| **Horizontally adjacent** (e.g., TL → TR) | **Yellow (4)** |
| **Vertically adjacent** (e.g., TL → BL) | **Green (3)** |

This rule is purely visual/logical — no arithmetic is involved.

### Rule 3 — Gap-Pattern Border (Topology + Conditional Logic)

This is the **trickiest rule**. After all reflections are placed, each quadrant that **originally contained** a sentinel gets a **full border** (all 4 sides) drawn in **azure (color 8)** — but the border is **NOT solid**. It has a **gap pattern** where some cells are filled and some are left as black (0), and the pattern depends on **which quadrant** the sentinel is in:

| Quadrant | Number | Gap Pattern | Meaning |
|---|---|---|---|
| **TR** | 1st | **1-1** | 1 cell filled, 1 cell gap, repeating |
| **TL** | 2nd | **2-2** | 2 cells filled, 2 cells gap, repeating |
| **BL** | 3rd | **3-3** | 3 cells filled, 3 cells gap, repeating |
| **BR** | 4th | **4-4** | 4 cells filled, 4 cells gap, repeating |

**How the pattern is applied:**

The border cells are traversed **clockwise** around the perimeter of the quadrant, starting from the **top-left corner** of that quadrant:
1. Top edge (left → right)
2. Right edge (top → bottom, skipping corner already counted)
3. Bottom edge (right → left, skipping corner already counted)
4. Left edge (bottom → top, skipping both corners already counted)

The fill/gap pattern is applied sequentially along this clockwise path. Only **background cells (color 0)** are overwritten — sentinel cells are preserved.

**Visual examples of each pattern (no sentinel interference):**

TR — 1st quad (1-1 pattern, alternating):
```
8 0 8 0 8
0 . . . 0
8 . . . 8
0 . . . 0
8 0 8 0 8
```

TL — 2nd quad (2-2 pattern, pairs):
```
8 8 0 0 8
. . . . 8
. . . . 0
. . . . 0
8 8 0 0 8
```

BL — 3rd quad (3-3 pattern, triplets):
```
8 8 8 0 0
. . . . 0
. . . . 8
. . . . 8
8 0 0 0 8
```

BR — 4th quad (4-4 pattern, blocks):
```
8 8 8 8 0
. . . . 0
. . . . 0
. . . . 0
. 8 8 8 8
```

Quadrants that were **originally empty** (and only received reflected copies) do **NOT** get any border at all.

---

## Training Examples Breakdown

### Example 1

- **Input:** Blue (1) sentinel at TL position (1,2). Red (2) sentinel at BR position (7,9).
- **Empty quadrants:** TR and BL.

**Reflections:**

| Source | Target | Relationship | Reflected Position | New Color |
|---|---|---|---|---|
| Blue(1) at TL | → TR | Horizontal | (1, 8) | Yellow (4) |
| Blue(1) at TL | → BL | Vertical | (9, 2) | Green (3) |
| Red(2) at BR | → TR | Vertical | (3, 9) | Green (3) |
| Red(2) at BR | → BL | Horizontal | (7, 1) | Yellow (4) |

**Gap-Pattern Borders:**

TL sentinel — **2nd quad → 2-2 pattern** (2 fill, 2 gap):
```
8  8 [1]  0  8      ← [1] = sentinel, not overwritten
0 [1][1][1] 8
0  0 [1]  0  0
8  0  0   0  0
8  0  0   8  8
```
Clockwise from (0,0): fill,fill,gap,gap,fill → fill,fill,gap,gap → fill,fill,gap,gap → fill,fill,gap,gap...

BR sentinel — **4th quad → 4-4 pattern** (4 fill, 4 gap):
```
8  8  8 [2]  0      ← first 4 fill (sentinel preserved), then gap
0  0 [2][2][2]      ← gaps continue
0  0  0 [2]  0
0  0  0  0   0
0  8  8  8   8      ← fill cycle restarts
```

### Example 2

- **Input:** Magenta (6) sentinel at TR position (1,7). Orange (7) sentinel at BL position (8,1).
- **Empty quadrants:** TL and BR.

**Reflections:**

| Source | Target | Relationship | Reflected Position | New Color |
|---|---|---|---|---|
| Magenta(6) at TR | → TL | Horizontal | (1, 3) | Yellow (4) |
| Magenta(6) at TR | → BR | Vertical | (9, 7) | Green (3) |
| Orange(7) at BL | → TL | Vertical | (2, 1) | Green (3) |
| Orange(7) at BL | → BR | Horizontal | (8, 9) | Yellow (4) |

**Gap-Pattern Borders:**

TR sentinel — **1st quad → 1-1 pattern** (alternating):
- Border traverses clockwise from top-left corner of TR quadrant (row 0, col 6)
- Pattern: fill, gap, fill, gap, fill, gap...
- Creates a checkerboard-like perimeter

BL sentinel — **3rd quad → 3-3 pattern** (3 fill, 3 gap):
- Border traverses clockwise from top-left corner of BL quadrant (row 6, col 0)
- Pattern: fill, fill, fill, gap, gap, gap, fill, fill, fill...

### Example 3

- **Input:** Maroon (9) sentinel at TL position (2,3). Magenta (6) sentinel at BL position (7,1).
- **Empty quadrants:** TR and BR.

**Reflections:**

| Source | Target | Relationship | Reflected Position | New Color |
|---|---|---|---|---|
| Maroon(9) at TL | → TR | Horizontal | (2, 7) | Yellow (4) |
| Maroon(9) at TL | → BR | Diagonal | (8, 7) | Maroon (9) — same |
| Magenta(6) at BL | → TR | Diagonal | (3, 9) | Magenta (6) — same |
| Magenta(6) at BL | → BR | Horizontal | (7, 9) | Yellow (4) |

**Gap-Pattern Borders:**

TL sentinel — **2nd quad → 2-2 pattern** (pairs):
- Groups of 2 filled cells separated by groups of 2 gaps around the perimeter

BL sentinel — **3rd quad → 3-3 pattern** (triplets):
- Groups of 3 filled cells separated by groups of 3 gaps around the perimeter

This example is particularly tricky because the solver sees TWO different gap patterns (2-2 and 3-3) in the same output and must figure out that the pattern size corresponds to the quadrant number.

---

## Test Case

- **Input:** Red (2) sentinel at TL position (3,2). Blue (1) sentinel at TR position (1,7).
- **Empty quadrants:** BL and BR.

**Reflections:**

| Source | Target | Relationship | Reflected Position | New Color |
|---|---|---|---|---|
| Red(2) at TL | → BL | Vertical | (7, 2) | Green (3) |
| Red(2) at TL | → BR | Diagonal | (7, 8) | Red (2) — same |
| Blue(1) at TR | → BL | Diagonal | (9, 3) | Blue (1) — same |
| Blue(1) at TR | → BR | Vertical | (9, 7) | Green (3) |

**Gap-Pattern Borders:**

TL sentinel — **2nd quad → 2-2 pattern** (pairs):
- Paired fill/gap border around TL quadrant
- Clockwise from (0,0): 8,8,0,0,8 → 8,0,0,0 → 8,8,2,0,8 → 0,0,0

TR sentinel — **1st quad → 1-1 pattern** (alternating):
- Alternating checkerboard-style border around TR quadrant
- Clockwise from (0,6): 8,0,8,0,8 → 0,8,0,8 → ...

Both use azure (8) color, but with **different gap patterns** — the solver must recognize that TR=1-1 (1st quad) and TL=2-2 (2nd quad).

**Important:** BL and BR were originally empty → they receive **only reflections, NO border at all**.

---

## Categories Used

| Category | How It's Used |
|---|---|
| **Geometric Transformation** | Reflection of sentinel positions across horizontal/vertical axes |
| **Pattern Recognition** | Identifying the plus-shaped sentinel pattern, the quadrant structure, and the gap-pattern in borders |
| **Object Detection** | Detecting which quadrants contain sentinels vs. which are empty |
| **Logic & Conditional Rules** | Color of reflected sentinel depends on source-target quadrant relationship; gap size depends on quadrant number |
| **Color Mapping** | Three distinct color behaviors: diagonal → same, horizontal → yellow, vertical → green |
| **Topology** | Gap-pattern border marks originally-occupied quadrants; pattern traverses clockwise around perimeter |
| **Counting** | The gap pattern size corresponds to the quadrant number (1st=1, 2nd=2, 3rd=3, 4th=4) — discovered by counting filled/gap cells |

---

## Why This Puzzle Is Hard

1. The solver must first **recognize the grid division** into quadrants via the gray cross.
2. They must **identify the reflection rule** — sentinels mirror into empty quadrants, not occupied ones.
3. They must **discover the color-change rule** — three different behaviors depending on quadrant adjacency type (horizontal/vertical/diagonal).
4. They must **notice the border has GAPS** — it's not a solid border, and the gap pattern is different for different quadrants.
5. They must **figure out the quadrant numbering** — TR=1st, TL=2nd, BL=3rd, BR=4th — which is non-obvious and requires carefully counting the filled vs. gap cells across multiple examples.
6. They must **understand the clockwise traversal** — the pattern starts at the top-left corner of each quadrant and flows clockwise around the perimeter, which affects exactly which cells are filled vs. gap.
7. All rules **interact with each other**: the border must not overwrite sentinel or reflected diamond cells, the gap pattern varies per quadrant, and the reflection colors add visual noise that can distract from the border pattern.
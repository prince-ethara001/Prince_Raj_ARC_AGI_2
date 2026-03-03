# Puzzle e9a47f2c — "Beacon Mirrors"

## Overview

An 11×11 grid is divided into **four quadrants** by a gray (5) cross-shaped divider at row 5 and column 5. Each quadrant may contain:

- A **beacon**: a plus/cross-shaped object made of 5 cells (center + 4 cardinal neighbors), all in one color.
- A **mirror marker**: a single cell of color 8.

The puzzle requires applying three layered rules to transform the input into the output.

---

## Rules

### Rule 1 — Reflection Across the Divider

Every beacon is **reflected** into the empty quadrant on the opposite side of one of the divider axes. The reflection maps each cell symmetrically across the divider line:

- **Vertical reflection** (across horizontal divider, row 5): row `r` maps to `5 + (5 - r)`, column stays the same. A top-half beacon reflects to the bottom-half (same column side), and vice versa.
- **Horizontal reflection** (across vertical divider, col 5): column `c` maps to `5 + (5 - c)`, row stays the same. A left-half beacon reflects to the right-half (same row band), and vice versa.

The reflected copy of the plus shape appears in the target quadrant. The **original beacon remains unchanged**.

### Rule 2 — Conditional Color Swap (Mirror Effect) — ODD/EVEN Cyclic Logic

If the **target quadrant** (where the reflection lands) contains a **mirror marker (color 8)**, the reflected beacon's color is **swapped** using the following **parity-based cyclic** rule:

**ODD colors cycle forward:**

```
1 (blue) → 3 (green) → 7 (orange) → 9 (maroon) → 1 (blue)
```

**EVEN colors cycle forward:**

```
2 (red) → 4 (yellow) → 6 (magenta) → 2 (red)
```

The underlying logic:
- Colors 0 (background), 5 (divider), and 8 (mirror) are **reserved** and never used as beacon colors.
- Among usable colors, **odd swaps with the next odd** and **even swaps with the next even** in a cyclic chain.
- The mirror marker (8) is **consumed** (removed) in the output.
- If the target quadrant has **no mirror**, the reflected beacon keeps its original color.

| Original Color | Parity | Swapped Color |
|---|---|---|
| 1 (blue) | ODD | 3 (green) |
| 3 (green) | ODD | 7 (orange) |
| 7 (orange) | ODD | 9 (maroon) |
| 9 (maroon) | ODD | 1 (blue) |
| 2 (red) | EVEN | 4 (yellow) |
| 4 (yellow) | EVEN | 6 (magenta) |
| 6 (magenta) | EVEN | 2 (red) |

### Rule 3 — Gravity Settling

After reflection and color mapping, the reflected beacon shape **drops downward** within its target quadrant as far as possible (like gravity). It stops when it reaches:

- The **bottom edge** of the quadrant, OR
- Another **occupied cell**.

The **original beacon does not move** — only the reflected copy settles.

---

## Training Example Walkthroughs

### Example 1 — Simple Reflection + Gravity (No Mirror)

- **Input:** Red (2) beacon at center (2,2) in top-left quadrant. All other quadrants empty. No mirrors.
- **Rule 1:** Reflects horizontally across the vertical divider → reflected center lands at (2,8) in top-right.
- **Rule 2:** No mirror in top-right → color stays red (2).
- **Rule 3:** Gravity drops the reflected shape 1 row down within top-right (rows 0–4). Final reflected center at (3,8).
- **Output:** Original red beacon unchanged in TL; red copy settled at (3,8) in TR.

### Example 2 — Reflection + ODD Color Swap + Gravity

- **Input:** Blue (1) beacon at (8,8) in bottom-right. Mirror (8) at (1,9) in top-right.
- **Rule 1:** Reflects vertically across the horizontal divider → reflected center at (2,8) in top-right.
- **Rule 2:** Mirror present in top-right → blue (1) is ODD, swaps to next ODD: **green (3)**. Mirror removed.
- **Rule 3:** Gravity drops green shape 1 row down. Final reflected center at (3,8).
- **Output:** Original blue beacon unchanged in BR; green (3) copy in TR; mirror gone.

### Example 3 — Two Beacons, One EVEN Swap

- **Input:** Magenta (6) beacon at (2,2) in TL, orange (7) beacon at (2,8) in TR. Mirror (8) at (9,1) in BL.
- **Rule 1:** Magenta reflects vertically to BL center (8,2). Orange reflects vertically to BR center (8,8).
- **Rule 2:** BL has mirror → magenta (6) is EVEN, swaps to next EVEN: **red (2)**. Mirror removed. BR has no mirror → orange stays (7).
- **Rule 3:** Both reflections drop 1 row down. Red settles at center (9,2) in BL. Orange settles at center (9,8) in BR.
- **Output:** Original beacons unchanged in top half; red (2) copy in BL; orange (7) copy in BR; mirror gone.

### Example 4 — Two Beacons, Two EVEN Swaps

- **Input:** Red (2) beacon at (2,2) in TL, yellow (4) beacon at (8,8) in BR. Mirror (8) at (0,8) in TR, mirror (8) at (10,1) in BL.
- **Rule 1:** Red reflects horizontally to TR center (2,8). Yellow reflects horizontally to BL center (8,2).
- **Rule 2:** TR has mirror → red (2) is EVEN, swaps to next EVEN: **yellow (4)**. Mirror removed. BL has mirror → yellow (4) is EVEN, swaps to next EVEN: **magenta (6)**. Mirror removed.
- **Rule 3:** Yellow (4) drops 1 row in TR, settling at center (3,8). Magenta (6) drops 1 row in BL, settling at center (9,2).
- **Output:** Original beacons unchanged; yellow (4) in TR; magenta (6) in BL; both mirrors gone.

> **Note for solver:** Example 4 demonstrates the full EVEN cycle: 2→4 and 4→6. Combined with Example 3 (6→2), the complete EVEN cycle is revealed: **2 → 4 → 6 → 2**.

---

## Test Case

- **Input:** Blue (1) beacon at (7,3) in BL, red (2) beacon at (7,8) in BR. Mirror (8) at (1,1) in TL.
- **Rule 1:** Blue reflects vertically to TL center (3,3). Red reflects vertically to TR center (3,8).
- **Rule 2:** TL has mirror → blue (1) is ODD, swaps to next ODD: **green (3)**. Mirror removed. TR has no mirror → red stays (2).
- **Rule 3:** Both shapes are already at the bottom of their quadrant (row 4 is max). No further drop.
- **Output:** Original beacons unchanged in bottom half; green (3) beacon in TL; red (2) copy in TR; mirror gone.

---

## Swap Evidence Across All Examples

| Example | Source Color | Parity | Mirror? | Result Color | Swap Shown |
|---|---|---|---|---|---|
| Train 1 | 2 (red) | EVEN | No | 2 (red) | No swap |
| Train 2 | 1 (blue) | ODD | Yes | 3 (green) | **1 → 3** |
| Train 3a | 6 (magenta) | EVEN | Yes | 2 (red) | **6 → 2** |
| Train 3b | 7 (orange) | ODD | No | 7 (orange) | No swap |
| Train 4a | 2 (red) | EVEN | Yes | 4 (yellow) | **2 → 4** |
| Train 4b | 4 (yellow) | EVEN | Yes | 6 (magenta) | **4 → 6** |
| Test | 1 (blue) | ODD | Yes | 3 (green) | **1 → 3** |
| Test | 2 (red) | EVEN | No | 2 (red) | No swap |

From the training data, a solver can fully reconstruct:
- **EVEN cycle:** 2→4 (Ex4a), 4→6 (Ex4b), 6→2 (Ex3a) ✓ Complete!
- **ODD cycle:** 1→3 (Ex2) — partial, but sufficient for the test case.

---

## Categories Used

| Category | How It Appears |
|---|---|
| **Geometric Transformations** | Reflection across divider axes |
| **Color Mapping** | ODD/EVEN parity-based cyclic color swap triggered by mirror markers |
| **Gravity / Physics** | Reflected shapes settle downward within quadrant bounds |
| **Object Detection** | Identifying beacons (plus shapes), mirrors (single 8 pixels), dividers (gray 5 cross) |
| **Logic & Conditional Rules** | Mirror presence triggers color change; mirror is consumed; parity determines swap cycle |
| **Topology** | Quadrant-based regions defined by divider structure; shapes confined within bounds |

---

## Color Index Reference

| Value | Color | Role |
|---|---|---|
| 0 | Black | Background |
| 1 | Blue | Beacon color (ODD) |
| 2 | Red | Beacon color (EVEN) |
| 3 | Green | Beacon color (ODD) |
| 4 | Yellow | Beacon color (EVEN) |
| 5 | Gray | Divider (reserved) |
| 6 | Magenta | Beacon color (EVEN) |
| 7 | Orange | Beacon color (ODD) |
| 8 | Azure | Mirror marker (reserved) |
| 9 | Maroon | Beacon color (ODD) |
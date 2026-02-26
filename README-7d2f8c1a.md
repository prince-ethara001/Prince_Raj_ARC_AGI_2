# Seed Bloom & Ghost Echo Puzzle - `hard_puzzle_001`

A custom ARC-AGI puzzle combining **4 interacting rules** - Object Detection, Geometric Pattern Generation, Color Mapping, and Mirror Ghost Reflection - into a single hard challenge.

---

## Overview

You are given a **12x12 black grid** containing exactly **3 single-pixel "seed" cells**, one each of:

- **Blue (1)**
- **Red (2)**
- **Green (3)**

Each seed **blooms** into a unique geometric pattern with specific colors, and then each pattern casts a **gray ghost copy** onto the opposite side of the grid.

The output is a grid with **6 visible patterns** - 3 real (colorful) + 3 ghosts (all gray).

---

## Color Palette

| Code | Color | Role |
|------|-------|------|
| `0` | Black | Empty / Background |
| `1` | Blue | Input seed (Plus shape) |
| `2` | Red | Input seed (X-diagonal shape) |
| `3` | Green | Input seed (3x3 Square) / Plus fill color |
| `4` | Yellow | X-diagonal fill color |
| `5` | Gray | Ghost echo color (Rule 4) |
| `6` | Magenta | Plus center color |
| `7` | Orange | X-diagonal center color |
| `8` | Azure | 3x3 Square fill color |
| `9` | Maroon | 3x3 Square center color |

---

## The Four Rules

Apply all four rules to transform the input into the output:

---

### Rule 1 - Seed Shape Generation

> Har seed apna unique pattern ugaata hai!

Each seed pixel blooms into a geometric shape. The **seed's color** determines **which shape** appears:

| Seed Color | Shape | Pattern |
|------------|-------|---------|
| Blue (`1`) | **Plus (+)** | Fills N, S, E, W (4 orthogonal neighbors) |
| Red (`2`) | **X Diagonal** | Fills NE, NW, SE, SW (4 diagonal neighbors) |
| Green (`3`) | **3x3 Square** | Fills all 8 surrounding cells (full box) |

**Visual:**

```
  Blue (1) -> Plus:       Red (2) -> X-diag:      Green (3) -> Square:

      . X .                 X . X                  X X X
      X X X                 . X .                  X X X
      . X .                 X . X                  X X X
```

---

### Rule 2 - Color Mapping

> Seed ka color badalta hai, pattern ka fill color alag hota hai!

Each shape uses a **different fill color** for its arms/body, and the **center pixel** transforms to a unique color:

| Seed | Fill Color (arms) | Center Color |
|------|--------------------|--------------|
| Blue (`1`) | Green (`3`) | Magenta (`6`) |
| Red (`2`) | Yellow (`4`) | Orange (`7`) |
| Green (`3`) | Azure (`8`) | Maroon (`9`) |

**Example - Blue seed at (row 5, col 5):**

```
  Before:                    After:
  . . . . . . .             . . . . . . .
  . . . . . . .             . . . . 3 . .      <- Green fill (N)
  . . . . 1 . .     ->      . . . 3 6 3 .      <- Green fill (W) + Magenta center + Green fill (E)
  . . . . . . .             . . . . 3 . .      <- Green fill (S)
  . . . . . . .             . . . . . . .
```

**Example - Red seed at (row 3, col 8):**

```
  Before:                    After:
  . . . . . . . . . . .    . . . . . . . 4 . 4 .    <- Yellow diag (NW, NE)
  . . . . . . . . 2 . .  -> . . . . . . . . 7 . .    <- Orange center
  . . . . . . . . . . .    . . . . . . . 4 . 4 .    <- Yellow diag (SW, SE)
```

**Example - Green seed at (row 7, col 4):**

```
  Before:                    After:
  . . . . . . .            . . . 8 8 8 .      <- Azure top row
  . . . . 3 . .    ->      . . . 8 9 8 .      <- Azure sides + Maroon center
  . . . . . . .            . . . 8 8 8 .      <- Azure bottom row
```

---

### Rule 3 - Conditional Placement

> Pattern sirf khali jagah pe lagta hai!

All 3 seed patterns are generated and placed onto the grid simultaneously. Since seeds are always well-spaced in this puzzle, the real patterns never overlap. But this rule becomes important for ghost placement (Rule 4).

- Real patterns are placed **first** (they have priority)
- If any pattern cell would go **outside the grid boundary**, it is simply skipped

---

### Rule 4 - Ghost Echo (THE HARD PART)

> Har pattern ki ek gray chhaayi (shadow) banti hai grid ke ulte kone pe!

After all 3 real patterns are placed, each pattern gets a **ghost copy** reflected through the **center point** of the grid.

**Ghost Position Formula:**

```
  ghost_row = 11 - original_row
  ghost_col = 11 - original_col
```

This is a **point reflection** through the grid center `(5.5, 5.5)`. In simple terms:

- **Top-left** seed -> ghost appears at **bottom-right**
- **Top-right** seed -> ghost appears at **bottom-left**
- **Bottom-left** seed -> ghost appears at **top-right**
- **Bottom-right** seed -> ghost appears at **top-left**

**Ghost Properties:**

| Property | Value |
|----------|-------|
| **Shape** | Same as the real pattern (Plus->Plus, X->X, Square->Square) |
| **Color** | ALL cells become Gray (`5`) - both fill and center |
| **Priority** | Ghost only fills cells that are still empty (`0`). It does NOT overwrite real patterns |

**Example - Blue seed at (2, 2):**

```
  REAL Plus at (2,2):              GHOST Plus at (9,9):

  Row 1: . . 3 . . . . . . . . .    Row 8:  . . . . . . . . . 5 . .
  Row 2: . 3 6 3 . . . . . . . .    Row 9:  . . . . . . . . 5 5 5 .
  Row 3: . . 3 . . . . . . . . .    Row 10: . . . . . . . . . 5 . .
```

The ghost is the **exact same Plus shape**, but mirrored to position `(9,9)` and painted entirely gray.

---

## Full Walkthrough - Train Example 0

### Input:

```
  Row  0: . . . . . . . . . . . .
  Row  1: . . . . . . . . . . . .
  Row  2: . . 1 . . . . . . . . .      <- Blue seed at (2,2)
  Row  3: . . . . . . . . . . . .
  Row  4: . . . . . . . . . . . .
  Row  5: . . . . . . . . . 2 . .      <- Red seed at (5,9)
  Row  6: . . . . . . . . . . . .
  Row  7: . . . . . . . . . . . .
  Row  8: . . . . . . . . . . . .
  Row  9: . . . . . 3 . . . . . .      <- Green seed at (9,5)
  Row 10: . . . . . . . . . . . .
  Row 11: . . . . . . . . . . . .
```

**3 Seeds found:**

| Seed | Position | Ghost Position `(11-r, 11-c)` |
|------|----------|-------------------------------|
| Blue (1) | `(2, 2)` | `(9, 9)` |
| Red (2) | `(5, 9)` | `(6, 2)` |
| Green (3) | `(9, 5)` | `(2, 6)` |

---

### Step 1: Generate Real Patterns

**Blue at (2,2) -> Plus with Green (3), center Magenta (6):**

```
  (1,2)=3   (2,1)=3  (2,2)=6  (2,3)=3   (3,2)=3
```

**Red at (5,9) -> X-diagonal with Yellow (4), center Orange (7):**

```
  (4,8)=4  (4,10)=4   (5,9)=7   (6,8)=4  (6,10)=4
```

**Green at (9,5) -> 3x3 Square with Azure (8), center Maroon (9):**

```
  (8,4)=8  (8,5)=8  (8,6)=8
  (9,4)=8  (9,5)=9  (9,6)=8
  (10,4)=8 (10,5)=8 (10,6)=8
```

---

### Step 2: Generate Ghost Copies (all Gray = 5)

**Ghost of Blue Plus at (9,9):**

```
  (8,9)=5   (9,8)=5  (9,9)=5  (9,10)=5   (10,9)=5
```

**Ghost of Red X-diagonal at (6,2):**

```
  (5,1)=5  (5,3)=5   (6,2)=5   (7,1)=5  (7,3)=5
```

**Ghost of Green 3x3 Square at (2,6):**

```
  (1,5)=5  (1,6)=5  (1,7)=5
  (2,5)=5  (2,6)=5  (2,7)=5
  (3,5)=5  (3,6)=5  (3,7)=5
```

---

### Step 3: Combine (Real patterns first, then Ghosts on empty cells)

### Output:

```
  Row  0: 0 0 0 0 0 0 0 0 0 0 0 0
  Row  1: 0 0 3 0 0 5 5 5 0 0 0 0      <- REAL Plus (green) + GHOST Square (gray)
  Row  2: 0 3 6 3 0 5 5 5 0 0 0 0      <- REAL Plus center + GHOST Square
  Row  3: 0 0 3 0 0 5 5 5 0 0 0 0      <- REAL Plus + GHOST Square
  Row  4: 0 0 0 0 0 0 0 0 4 0 4 0      <- REAL X-diagonal (yellow)
  Row  5: 0 5 0 5 0 0 0 0 0 7 0 0      <- GHOST X-diag (gray) + REAL X center (orange)
  Row  6: 0 0 5 0 0 0 0 0 4 0 4 0      <- GHOST X center (gray) + REAL X-diagonal
  Row  7: 0 5 0 5 0 0 0 0 0 0 0 0      <- GHOST X-diagonal (gray)
  Row  8: 0 0 0 0 8 8 8 0 0 5 0 0      <- REAL Square (azure) + GHOST Plus (gray)
  Row  9: 0 0 0 0 8 9 8 0 5 5 5 0      <- REAL Square center + GHOST Plus (gray)
  Row 10: 0 0 0 0 8 8 8 0 0 5 0 0      <- REAL Square + GHOST Plus (gray)
  Row 11: 0 0 0 0 0 0 0 0 0 0 0 0
```

Notice how:
- **Top-left** has the REAL Blue Plus (colored) and nearby the GHOST of Green Square (gray)
- **Right side** has the REAL Red X-diagonal (colored)
- **Middle-left** has the GHOST of Red X-diagonal (gray)
- **Bottom-center** has the REAL Green Square (colored)
- **Bottom-right** has the GHOST of Blue Plus (gray)

---

## What Makes This Hard

| Challenge | Why It's Difficult |
|-----------|--------------------|
| **4 simultaneous rules** | Solver must discover shape generation, color mapping, conditional placement, AND ghost reflection all at once |
| **7 output colors** | Colors 3, 4, 5, 6, 7, 8, 9 all appear - high information density |
| **Input != Output colors** | Blue (1) input produces Green (3) + Magenta (6) output - nothing is obvious |
| **Ghost is misleading** | Gray shapes look random until you realize they're mirrored copies of the real patterns |
| **Point reflection** | The mirror formula `(11-r, 11-c)` is not immediately intuitive - requires studying multiple examples |
| **Shape != Shape color** | Plus shape is green, not blue. X shape is yellow, not red. Square is azure, not green. Every mapping is indirect |
| **Ghost priority rule** | Ghosts don't overwrite real patterns - adds another layer of conditional logic |
| **3 examples only** | Just 3 training pairs to figure out all 4 rules from |

---

## How to Play

1. **Load the puzzle** - Open `data/prince-training/hard_puzzle_001.json` in the [ARC testing interface](https://arcprize.org/play) or any compatible viewer.

2. **Study the 3 training pairs** - Each pair has 3 seeds (one Blue, one Red, one Green) at different positions. Compare inputs to outputs carefully.

3. **Discover the rules** - Key observations to make:
   - Each seed color always produces the same shape
   - Each shape always uses the same fill + center colors
   - There are mysterious gray shapes scattered around
   - The gray shapes mirror the colored shapes through the grid center

4. **Solve the 2 test inputs** - Apply all 4 rules mentally or on paper and construct the output grids.

5. **Submit** - Build your answer in the grid editor and hit **Submit!**

---

## Solving Strategy

### Step-by-step approach:

1. **Find the 3 seeds** in the input and note their positions and colors.

2. **Generate the real patterns:**
   - Blue -> Plus (+) with Green fill, Magenta center
   - Red -> X diagonal with Yellow fill, Orange center
   - Green -> 3x3 Square with Azure fill, Maroon center

3. **Place real patterns** on the grid.

4. **Compute ghost positions** - For each seed at `(r, c)`:
   - Ghost center = `(11 - r, 11 - c)`

5. **Generate ghost patterns** - Same shape as real, but ALL cells are Gray (5).

6. **Place ghosts** - Only fill cells that are still empty (0). Don't overwrite real patterns.

7. That's your output!

---

## Training & Test Pairs Summary

| Pair | Blue (1) Seed | Red (2) Seed | Green (3) Seed |
|------|---------------|--------------|----------------|
| Train 0 | `(2, 2)` -> ghost `(9, 9)` | `(5, 9)` -> ghost `(6, 2)` | `(9, 5)` -> ghost `(2, 6)` |
| Train 1 | `(4, 2)` -> ghost `(7, 9)` | `(1, 8)` -> ghost `(10, 3)` | `(9, 6)` -> ghost `(2, 5)` |
| Train 2 | `(8, 9)` -> ghost `(3, 2)` | `(6, 2)` -> ghost `(5, 9)` | `(2, 5)` -> ghost `(9, 6)` |
| **Test 0** | `(8, 6)` -> ghost `(3, 5)` | `(2, 9)` -> ghost `(9, 2)` | `(5, 2)` -> ghost `(6, 9)` |
| **Test 1** | `(3, 3)` -> ghost `(8, 8)` | `(9, 1)` -> ghost `(2, 10)` | `(6, 8)` -> ghost `(5, 3)` |

> Note: Seed positions vary across all examples. The seed order (which color appears where) also changes - the solver must truly understand the rules, not memorize positions.

---

## Puzzle Categories Used

This puzzle combines **four** cognitive ability categories from ARC-AGI:

1. **Object Detection** - Identify the 3 seed pixels and their colors in a sparse 12x12 grid

2. **Pattern Recognition** - Recognize that each seed color always generates the same geometric shape (Plus, X, or Square)

3. **Color Changes** - Map input seed colors to completely different output colors (1->3+6, 2->4+7, 3->8+9)

4. **Logic & Rules** - Discover the ghost mirror rule: point reflection through grid center, gray color, conditional placement on empty cells only

---

## File Location

```
ARC-AGI-2/
└── data/
    └── prince-training/
        ├── hard_puzzle_001.json           <- the puzzle file
        └── README-hard_puzzle_001.md      <- this file
```

---

## Quick Reference Card

```
+-----------------------------------------------------------+
|  GRID: 12x12 with 3 seeds (one each of color 1, 2, 3)    |
|                                                           |
|  +-----------------------------------------------------+ |
|  |  RULES 1-2: SEED -> SHAPE + COLORS                  | |
|  |                                                     | |
|  |  Blue (1)  -> Plus (+)    fill=Green(3)  ctr=Mag(6) | |
|  |  Red  (2)  -> X-diagonal  fill=Yell(4)  ctr=Org(7) | |
|  |  Green(3)  -> 3x3 Square  fill=Azure(8) ctr=Mar(9) | |
|  +-----------------------------------------------------+ |
|                                                           |
|  +-----------------------------------------------------+ |
|  |  RULE 3: PLACEMENT PRIORITY                          | |
|  |  Real patterns placed first (always win)             | |
|  |  Ghosts placed second (only on empty cells)          | |
|  +-----------------------------------------------------+ |
|                                                           |
|  +-----------------------------------------------------+ |
|  |  RULE 4: GHOST ECHO                                  | |
|  |  Ghost position = (11 - row, 11 - col)               | |
|  |  Ghost shape    = same as real pattern                | |
|  |  Ghost color    = ALL Gray (5)                        | |
|  |  Ghost priority = only fills empty (0) cells          | |
|  +-----------------------------------------------------+ |
|                                                           |
|  OUTPUT COLORS: 3,4,5,6,7,8,9  (7 unique non-zero)       |
|  REAL patterns: colorful  |  GHOST patterns: all gray     |
+-----------------------------------------------------------+
```

---

*Seed it, bloom it, ghost it!*
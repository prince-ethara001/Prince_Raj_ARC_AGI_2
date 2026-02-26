# Lattice Bloom Puzzle - `e141c1db`

A custom ARC-AGI puzzle featuring a **dense, colorful mosaic** of rooms separated by walls. No divider line, no top/bottom split - the entire grid is a rich lattice that transforms based on three interacting rules.

---

## Overview

You are given a **22x22 grid** that looks like a **walled maze** or **window lattice**:

- **Maroon (9) walls** form horizontal and vertical lines, creating a grid of rectangular **"rooms"**
- Most rooms are **empty (black/0)**, but some contain a single colored **"seed"** pixel
- A few rooms may contain **two seeds** of different colors

Your job: figure out what happens to the grid when the three transformation rules are applied.

> The output is a **dense, colorful mosaic** - nearly the entire grid fills with color. It looks dramatically different from the sparse input!

---

## Visual Layout

### Input (sparse)
```
9 9 9 9 9 9 9 9 9 9 9 9 9    <- Maroon walls everywhere
9 0 0 0 0 9 0 0 0 0 0 9 ...    <- Empty rooms (black)
9 0 0 1 0 9 0 2 0 0 0 9 ...    <- Rooms with seed pixels
9 0 0 0 0 9 0 0 0 0 0 9 ...
9 9 9 9 9 9 9 9 9 9 9 9 9    <- Another wall line
9 0 0 0 0 9 0 0 0 0 0 9 ...
...
```

### Output (dense & colorful)
```
9 9 9 9 9 9 9 9 9 9 9 9 9
9 1 1 1 1 8 2 2 2 2 2 8 ...    <- Rooms flooded with seed color!
9 1 1 1 1 8 2 2 2 2 2 8 ...    <- Walls changed to azure (8)!
9 1 1 1 1 8 2 2 2 2 2 8 ...
9 8 8 8 8 9 8 8 8 8 8 9 ...    <- Some walls stay maroon (9)
9 5 5 5 5 8 3 3 3 3 3 8 ...    <- More colored rooms
...
```

---

## Color Palette

| Code | Color | Role |
|------|-------|------|
| `0` | Black | Empty room (no seed) |
| `1` | Blue | Seed / Room fill color |
| `2` | Red | Seed / Room fill color |
| `3` | Green | Seed / Room fill color |
| `4` | Yellow | Isolation marker (Rule 3) |
| `5` | Gray | Seed / Room fill color |
| `6` | Magenta | Seed / Room fill color |
| `7` | Orange | Hot wall - borders 3+ distinct room colors (Rule 2) |
| `8` | Azure | Warm wall - borders exactly 2 distinct room colors (Rule 2) |
| `9` | Maroon | Cold wall - borders 0-1 distinct room colors (stays original) |

---

## The Three Rules

Apply these rules **in order** to transform the input into the output:

---

### Rule 1 - Seed Bloom

> *Beej se poora kamra rang jaata hai!*

Find each enclosed **room** - a connected region of non-wall cells bounded by maroon (9) walls. Look for **seed pixels** inside each room (any non-zero, non-wall colored cell):

| Seeds in Room | What Happens |
|---------------|-------------|
| **No seeds** | Room stays black (0) - no bloom |
| **Exactly 1 seed** | Entire room floods with that seed's color |
| **2+ seeds** | Entire room floods with the **maximum** seed color value |

**Example:** A room containing a single Blue (1) seed at position (3,3):

```
Before:                    After:
9 0 0 0 0 9              9 1 1 1 1 9
9 0 0 0 0 9              9 1 1 1 1 9
9 0 0 1 0 9     ->       9 1 1 1 1 9
9 0 0 0 0 9              9 1 1 1 1 9
9 0 0 0 0 9              9 1 1 1 1 9
```

**Example:** A room with two seeds - Green (3) and Blue (1):

```
Before:                    After:
9 0 0 3 0 9              9 3 3 3 3 9
9 0 1 0 0 9     ->       9 3 3 3 3 9    (max(3,1) = 3 -> Green)
9 0 0 0 0 9              9 3 3 3 3 9
```

---

### Rule 2 - Wall Glow

> *Deewar chamakti hai jab alag rang milte hain!*

After all rooms have bloomed, examine each **wall cell** (maroon/9). Count how many **distinct non-zero, non-wall fill colors** are orthogonally adjacent to it:

| Adjacent Distinct Colors | Wall Becomes |
|--------------------------|-------------|
| **3 or more** | Orange (7) - "hot wall" |
| **Exactly 2** | Azure (8) - "warm wall" |
| **0 or 1** | Stays Maroon (9) - "cold wall" |

**How it works:** A wall cell between a Blue (1) room and a Red (2) room touches 2 distinct colors -> becomes Azure (8).

A wall cell at the intersection of a Blue (1), Red (2), and Green (3) room -> touches 3 distinct colors -> becomes Orange (7).

A wall cell that only borders one colored room (or no rooms / only empty rooms) -> stays Maroon (9).

**Example:** Wall between Blue room (left) and Red room (right):

```
Before bloom:     After bloom:       After wall glow:
... 1 1 9 2 2 ...   ... 1 1 9 2 2 ...    ... 1 1 8 2 2 ...
                                          ^
                                    Wall sees Blue(1) left + Red(2) right
                                    = 2 distinct colors -> Azure (8)
```

> Note: Only considers orthogonal neighbors (up, down, left, right). Diagonal doesn't count.
> Note: Black (0) rooms and other wall cells are NOT counted as colors.

---

### Rule 3 - Isolation Mark

> *Akela rang special hai - usse mark karo!*

After wall glow, look at all the bloomed rooms. If any room's fill color is **unique** across the entire grid - meaning NO other room shares that same fill color - place a **Yellow (4)** marker at the **geometric center** of that room.

> **Key constraint:** Rooms that receive yellow markers always have **odd dimensions** (e.g., 5x5, 3x5, 5x3) so the center cell is always unambiguous - there's exactly one middle cell.

**Center calculation (for odd-dimension rooms):**
- `center_row = min_row + height // 2`
- `center_col = min_col + width // 2`
- For a **5x5** room starting at row 1, col 1: center = **(3, 3)** - the 3rd row and 3rd col of the room.

**Example 1:** A 5x5 room filled with Gray (5) that is unique - center at row 3, col 3 of the room:

```
Before:                    After:
9 5 5 5 5 5 9            9 5 5 5 5 5 9
9 5 5 5 5 5 9            9 5 5 5 5 5 9
9 5 5 5 5 5 9     ->     9 5 5 4 5 5 9    <- Yellow at exact center!
9 5 5 5 5 5 9            9 5 5 5 5 5 9
9 5 5 5 5 5 9            9 5 5 5 5 5 9
```

**Example 2:** A 5x5 room filled with Magenta (6) that is unique - same logic:

```
Before:                    After:
9 6 6 6 6 6 9            9 6 6 6 6 6 9
9 6 6 6 6 6 9            9 6 6 6 6 6 9
9 6 6 6 6 6 9     ->     9 6 6 4 6 6 9    <- Yellow center!
9 6 6 6 6 6 9            9 6 6 6 6 6 9
9 6 6 6 6 6 9            9 6 6 6 6 6 9
```

> Note: If a color appears in 2+ rooms, NONE of those rooms get a yellow marker.
> Note: Empty (black) rooms never get a marker.
> Note: Only odd-dimension rooms get unique colors in this puzzle, so the center is always exact.

---

## How to Play

1. **Load the puzzle** - Open `/training/e141c1db.json` in the [ARC testing interface](https://arcprize.org/play) or any compatible viewer.

2. **Study all 3 training pairs** - Notice:
   - Sparse inputs with scattered seed pixels -> Dense colorful outputs
   - Wall colors change between input and output
   - Some rooms have yellow dots, most don't

3. **Discover the rules** - The key observations:
   - Each seed pixel "blooms" to fill its entire room
   - Wall segments change color based on what's on either side
   - Rare/unique room colors get a special yellow center mark

4. **Solve the test** - Apply the three rules to the test input and construct the output grid.

5. **Submit** - Build your answer in the grid editor and hit **Submit!**

---

## Solving Strategy

### Step-by-step:

1. **Identify all rooms** - Trace the maroon walls to find each enclosed rectangular area.

2. **Find seeds** - Look for any non-zero, non-9 pixel inside each room. Most rooms have exactly one seed (or none).

3. **Bloom** - Fill each room entirely with its seed color. Empty rooms stay black. Multi-seed rooms use the max color value.

4. **Glow the walls** - For each wall segment:
   - Check what colored rooms are on either side
   - 2 different colors touching? -> Azure (8)
   - 3+ different colors touching? -> Orange (7)
   - Otherwise -> stays Maroon (9)

5. **Mark isolates** - Count how many rooms have each fill color. Any color appearing exactly once? Put a Yellow (4) dot in the center of that room.

---

## What Makes This Hard

| Challenge | Why |
|-----------|-----|
| **Visual transformation is dramatic** | Input is mostly black with tiny seeds; output is a dense colorful mosaic. Hard to see the connection at first glance. |
| **Three interacting rules** | Must discover and correctly layer all 3 rules - bloom, glow, and mark |
| **Wall color depends on neighbors** | Wall glow requires understanding which rooms border which walls - spatial reasoning across the grid |
| **Isolation is a global property** | The yellow marker rule requires counting colors across ALL rooms - can't be solved locally |
| **Yellow center requires odd rooms** | Must identify the exact center cell of odd-dimensioned rooms (5x5 -> row 3, col 3) |
| **Consistent lattice layout** | All pairs use the same wall configuration -> rules must be discovered from subtle output differences |
| **Density** | Output is 90-93% filled -> lots of cells to get right |

---

## Training Pairs Summary

| Pair | Grid | Rooms (5x5 / other) | Seeded Rooms | Empty Rooms | Unique Colors | Yellow Markers |
|------|------|---------------------|-------------|-------------|---------------|----------------|
| Train 0 | 22x22 | 9 x 5x5 + 7 smaller | 11 | 5 | Gray(5), Magenta(6) | 2 |
| Train 1 | 22x22 | 9 x 5x5 + 7 smaller | 11 | 5 | Gray(5), Magenta(6) | 2 |
| Train 2 | 22x22 | 9 x 5x5 + 7 smaller | 12 | 4 | none unique | 0 |
| **Test** | 22x22 | 9 x 5x5 + 7 smaller | 11 | 5 | Red(2), Green(3) | 2 |

> **Train 0 & Train 1** both demonstrate Rule 3 (yellow markers) with Gray(5) and Magenta(6) as unique colors, while **Train 2** shows that when no color is unique, no yellow markers appear. The **Test** uses different unique colors - Red(2) and Green(3) - to ensure the solver generalizes the rule rather than memorizing specific colors. All yellow markers land in **5x5 odd-dimension rooms** at the exact center cell.

---

## Puzzle Categories Used

This puzzle combines **three** cognitive ability categories from ARC-AGI:

1. **Object Detection + Pattern Recognition** - Identifying rooms (enclosed regions) and their seed pixels within a complex lattice structure

2. **Color Changes** - Wall glow rule maps wall colors based on the relationship between adjacent room fills; seed bloom maps a single pixel to fill an entire region

3. **Counting + Logic & Rules** - Isolation mark requires counting color occurrences globally, then applying a conditional transformation (unique -> mark, non-unique -> skip)

---

## File Location

```
ARC-AGI-2/
    └── /training/
        ├── 7d2f8c1a.json           <- the puzzle file
    ├── evaluation/
        └── 7d2f8c1a.json           <- evaluation copy
    └── README-7d2f8c1a.md          <- readme.md file

```

---

## Quick Reference Card

```
+-----------------------------------------------------+
|  GRID: 22x22 dense lattice - NO divider line        |
|  WALLS: rows/cols {0,6,12,18,21} -> 5x5 rooms      |
|  SEEDS: Colored pixels inside rooms                 |
|                                                     |
|  +---------------------------------------------+   |
|  |  RULE 1: SEED BLOOM                         |   |
|  |  0 seeds -> room stays black                |   |
|  |  1 seed  -> room fills with that color      |   |
|  |  2+ seeds -> room fills with max(colors)    |   |
|  +---------------------------------------------+   |
|  |  RULE 2: WALL GLOW                          |   |
|  |  Wall touches 2 distinct colors -> Azure(8) |   |
|  |  Wall touches 0-1 colors -> stays Maroon(9) |   |
|  +---------------------------------------------+   |
|  |  RULE 3: ISOLATION MARK                     |   |
|  |  Room with globally unique color ->          |   |
|  |    Yellow(4) at exact center of 5x5 room    |   |
|  |    (row 3, col 3 of the room)               |   |
|  |  Non-unique colors -> no mark               |   |
|  +---------------------------------------------+   |
|                                                     |
|  RESULT: Dense colorful mosaic with glowing walls   |
|          and occasional yellow center marks          |
+-----------------------------------------------------+
```

---

*Let the lattice bloom!*

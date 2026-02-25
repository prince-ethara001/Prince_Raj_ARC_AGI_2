# Topology Painter Puzzle - `92cedc96`

A custom ARC-AGI puzzle with a single rule that's deceptively hard to discover - cells are colored based on their **topological position** within a shape, not by matching to a template.

---

## 📖 Overview

You are given a **28×28 grid** divided into two sections by an **azure divider line** (row 8):

- **Top section (Examples, rows 0-7):** Several large, detailed colored figures - a Robot, a Castle, a Spaceship, a Tree - that **demonstrate the coloring rule** in action. These are your only clues.
- **Bottom section (Canvas, rows 9-27):** Gray silhouettes (`5`) of **completely different shapes** - a Crown, a Diamond, a Hammer, a Shield, a Sword, an Anchor - shapes you've never seen in the examples. You must color them using the same rule.

> ⚠️ This is NOT a "match the shape and copy the colors" puzzle. The canvas shapes are **entirely different** from the example shapes. You must **discover the underlying coloring rule** from the examples and apply it to novel shapes.

---

## The Example Figures (Top Section)

These chunky, detailed figures teach you the rule:

### Robot (7×5)

```
· X X X ·       A person/android figure with a head,
· X X X ·       outstretched arms, narrow torso,
X X X X X       wide hips, and two separate legs.
· · X · ·
· X X X ·
· X · X ·
· X · X ·
```

### Castle (7×7)

```
X · X · X · X   A castle with four battlements on top,
X X X X X X X   a wide fortified wall, narrowing body,
· X X X X X ·   and a broad base.
· · X X X · ·
· · X X X · ·
· X X X X X ·
· X X X X X ·
```

### Spaceship (6×7)

```
· · · X · · ·   A pointed nose at the top, widening
· · X X X · ·   body, full-width wings, and two
· X X X X X ·   separate landing gear pods at the
X X X X X X X   bottom.
· · X · X · ·
· X · · · X ·
```

### Tree (7×7)

```
· · · X · · ·   A triangular canopy that widens from
· · X X X · ·   a point to full width, then a narrow
· X X X X X ·   trunk, and roots spreading at the base.
X X X X X X X
· · · X · · ·
· · · X · · ·
· · X X X · ·
```

---

## The Color Palette

| Code | Color | Meaning (to discover!) |
|---|---|---|
| `0` | ⬛ Black | Empty / Background |
| `1` | 🔵 Blue | ??? |
| `2` | 🔴 Red | ??? |
| `3` | 🟢 Green | ??? |
| `4` | 🟡 Yellow | ??? |
| `5` | ⬜ Gray | Unpainted silhouette (input only) |
| `8` | 🔷 Azure | Divider line |

---

## The Single Rule

**This section contains the answer - try to figure it out yourself first!**

<details>
<summary>Click to reveal the rule (SPOILER)</summary>

### Neighbor-Count Coloring

Each cell in a figure is colored based on **how many orthogonal neighbors** (up, down, left, right) it has that are **also part of the same figure**:

| Neighbors | Position Type | Color |
|---|---|---|
| **1** neighbor | Tip / Endpoint | 🔴 Red (`2`) |
| **2** neighbors | Corner / Chain link | 🟡 Yellow (`4`) |
| **3** neighbors | T-junction / Edge | 🟢 Green (`3`) |
| **4** neighbors | Fully surrounded / Interior | 🔵 Blue (`1`) |

That's it - **one rule**. But figuring it out takes careful observation!

### Visual Breakdown

**Robot's head (rows 0-1):**

```
Position:     · X X X ·       Neighbors:    · 2 3 2 ·       Colored:    · Y G Y ·
              · X X X ·                     · 3 4 3 ·                   · G B G ·
```

The center cell of the head has 4 neighbors (up, down, left, right all filled) → Blue.
The corner cells of the head have 2 neighbors each → Yellow.
The edge cells have 3 neighbors → Green.

**Robot's legs (rows 5-6):**

```
Position:     · X · X ·       Neighbors:    · 2 · 2 ·       Colored:    · Y · Y ·
              · X · X ·                     · 1 · 1 ·                   · R · R ·
```

The feet (bottom of each leg) have only 1 neighbor (the cell above) → Red.
The upper leg cells have 2 neighbors (above and below) → Yellow.

**Castle's battlements (row 0):**

```
Position:     X · X · X · X   Neighbors:    1 · 1 · 1 · 1   Colored:    R · R · R · R
```

Each battlement tip has only 1 neighbor (the cell below) → Red.

**A 3×3 solid block:**

```
Position:     X X X     Neighbors:    2 3 2     Colored:    Y G Y
              X X X                   3 4 3                 G B G
              X X X                   2 3 2                 Y G Y
```

The center (4 neighbors) → Blue. Edges (3) → Green. Corners (2) → Yellow.

</details>

---

## How to Discover the Rule

Here's the thought process a solver typically goes through:

1. **First instinct:** "Maybe I match gray shapes to the colored examples?" → ❌ **Nope.** The canvas shapes (Crown, Diamond, Hammer, Shield, Sword, Anchor) are completely different from the examples (Robot, Castle, Spaceship, Tree).

2. **Second attempt:** "Maybe the colors depend on position - like top=red, middle=something?" → ❌ **Nope.** Same row can have different colors. Red appears at tips everywhere, not just at the top or bottom.

3. **Key observation:** Look at the RED cells. They always appear at the **pointy tips** - the Robot's feet, the Castle's battlements, the Spaceship's nose, the Tree's top point. What do tips have in common? They only touch ONE other cell.

4. **Next observation:** Look at the BLUE cells. They always appear **deep inside** big solid regions - the middle of the Castle's walls, the core of the Spaceship's body. These cells are **completely surrounded** on all 4 sides.

5. **The breakthrough:** *"It's about how many neighbors each cell has within the figure!"*

6. **Verify:** Count neighbors for every cell in the example figures - it matches perfectly every time.

7. **Apply:** Now count neighbors for each cell in the gray shapes and assign colors.

> This typically takes **3-5 minutes** of careful study. The rule is simple once you see it, but discovering it requires genuine spatial reasoning across these large, complex figures.

---

## The Canvas Shapes (Bottom Section)

These are the shapes you must color. They never appear in the examples:

### Crown (6×9)

```
X · · X · · X · ·
X X · X X · X X ·
· X X X X X X X ·
· · X X X X X · ·
· · X X X X X · ·
· · X X X X X · ·
```

### Diamond (7×7)

```
· · · X · · ·
· · X X X · ·
· X X X X X ·
X X X X X X X
· X X X X X ·
· · X X X · ·
· · · X · · ·
```

### Hammer (7×5)

```
X X X X X
X X X X X
· · X · ·
· · X · ·
· · X · ·
· · X · ·
· · X · ·
```

### Shield (7×7)

```
· X X X X X ·
X X X X X X X
X X X X X X X
X X X X X X X
· X X X X X ·
· · X X X · ·
· · · X · · ·
```

### Sword (8×3)

```
· X ·
X X X
· X ·
· X ·
· X ·
· X ·
X X X
· X ·
```

### Anchor (7×7)

```
· · · X · · ·
· · · X · · ·
X X X X X X X
· · · X · · ·
· · · X · · ·
· · X X X · ·
· X X · X X ·
```

---

## What Makes This Hard

| Challenge | Why |
|---|---|
| **No shape matching** | Canvas shapes are completely new - you can't just "find and copy" |
| **Colors seem random** | At first glance, the coloring looks arbitrary across these big figures |
| **Requires abstraction** | You must discover an abstract topological rule from visual examples |
| **Big figures = more noise** | Large detailed shapes make the pattern harder to spot than tiny ones |
| **Spatial counting** | Applying the rule to 7×7 or 6×9 shapes requires careful neighbor counting |
| **Red herrings** | You might waste time trying symmetry, position-based coloring, shape matching, etc. |
| **Single rule, deep insight** | There's no multi-step process - you either "see it" or you don't |

---

## How to Play

1. **Load the puzzle** - Open `/training/92cedc96.json` in the [ARC testing interface](https://arcprize.org/play) or any compatible viewer.

2. **Study the examples** - Look at the large colored figures above the azure divider line (Robot, Castle, Spaceship, Tree). These teach you the rule. Don't just glance - really analyze *why* each cell has its specific color.

3. **Crack the rule** - Figure out what determines each cell's color. (Hint: it's about the cell's relationship to its immediate neighbors within the same shape.)

4. **Apply to canvas** - Below the divider, you'll see gray silhouettes (Crown, Diamond, Hammer, Shield, Sword, Anchor). Apply the rule you discovered to color every gray cell correctly.

5. **Submit** - Build your answer and hit **Submit!**

---

## Training Pairs Summary

| Pair | Example Figures (top) | Canvas Shapes (bottom) |
|---|---|---|
| Train 0 | Robot, Castle, Spaceship | Crown, Hammer, Diamond |
| Train 1 | Spaceship, Tree, Robot | Shield, Anchor, Sword |
| Train 2 | Castle, Robot, Tree | Hammer, Shield, Anchor |
| **Test** | Tree, Castle, Spaceship | Crown, Sword, Diamond |

> Note: The example figures rotate between pairs (different subsets, different positions), but the **coloring rule stays exactly the same**. This is a crucial clue that the rule is universal - it doesn't depend on which figure you're looking at.

---

## File Location

```
PRINCE_RAJ_ARC-AGI-2/
    ├── training/
    │   └── 92cedc96.json          ← the puzzle file
    ├── README-92cedc96.md         ← the readme file
```

---

## Quick Reference Card

```

┌──────────────────────────────────────────────────────┐
│  THE ONE RULE:                                       │
│                                                      │
│  Color each cell by its orthogonal neighbor count    │
│  (neighbors that are also part of the same figure):  │
│                                                      │
│    1 neighbor  (tip)      → Red    (2)  🔴           │ 
│    2 neighbors (corner)   → Yellow (4)  🟡           │ 
│    3 neighbors (edge)     → Green  (3)  🟢           │ 
│    4 neighbors (interior) → Blue   (1)  🔵           │ 
└──────────────────────────────────────────────────────┘
```

---

*Count your neighbors, paint your truth! *
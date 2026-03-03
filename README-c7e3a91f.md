# Puzzle c7e3a91f — "Holes in Shapes" (Hard Version)

## Overview

A 30×30 grid contains several **beautiful filled shapes** (crowns, diamonds, hearts, shields, crosses, arrows, stars, hexagons, bells, goblets, pentagons, mushrooms, trees, hourglasses) each drawn in a unique color on a black (0) background. Each shape has **holes** punched inside it — black pixels (0) within the shape's body. The transformation involves a **3-rule system** that recolors the entire shape in a non-obvious way.

---

## Why This Puzzle Is Hard

Unlike a simple "extract the holes" puzzle, the **output still contains full shapes** — they don't disappear. Instead:

- All colors change in a coordinated but non-obvious way
- The shape's original color moves INTO the holes
- The shape's body gets a NEW color determined by hole count
- A maroon (9) border wraps every shape

A solver cannot just "spot what's missing." They must figure out three interacting recolor rules simultaneously.

---

## Categories Used

| Category | How It's Used |
|---|---|
| **Object Detection** | Identify each distinct filled shape by its color and boundary |
| **Topology** | Detect enclosed holes (black pixels fully surrounded by shape body) |
| **Counting** | Count holes inside each shape — this determines the new body color |
| **Color Mapping** | Three-way color swap: body→count-color, holes→original-color, border→9 |
| **Logic & Conditional Rules** | Three layered if-then rules interact to produce the output |
| **Pattern Recognition** | Recognizing that the same 3-rule system applies to every shape regardless of type |

---

## Rules

### Rule 1 — INVERSION (Color Swap Between Body and Holes)

The shape's body pixels and hole pixels **swap roles**:
- **Hole pixels** (which were black/0 in the input) become the shape's **ORIGINAL color**
- **Body pixels** (which were the shape's color in the input) become a **NEW color** (determined by Rule 2)

### Rule 2 — HOLE COUNT → NEW BODY COLOR

Count the total number of hole pixels inside each shape. That count **becomes the new body color**:

| Number of Holes | New Body Color |
|---|---|
| 1 | 1 (blue) |
| 2 | 2 (red) |
| 3 | 3 (green) |
| 4 | 4 (yellow) |
| 5 | 5 (gray) |
| 6 | 6 (magenta) |
| 7 | 7 (orange) |

### Rule 3 — BORDER OUTLINE (Color 9)

Every shape gets a **maroon border** (color 9). Specifically, any pixel of the shape (body or hole) that is **adjacent to the outside** (touches a non-shape pixel or the grid edge via cardinal directions) gets overwritten with color **9**. This is drawn ON TOP of the Rule 1+2 recoloring.

---

## Rule Interaction & Layering

Rules apply in strict order: **Rule 1+2 first, then Rule 3 on top.**

1. First, every body pixel of a shape is recolored to the hole-count color (Rule 2)
2. Simultaneously, every hole pixel is filled with the shape's original color (Rule 1)
3. Finally, all outermost perimeter pixels are overwritten with 9 (Rule 3)

This means the **border hides** some of the recolored body pixels, adding visual noise that makes pattern extraction harder.

---

## Walkthrough: Training Example 1

### Shape 1: Crown (original color 8, azure)
- **Holes**: 3 holes in an L-shape at positions (5,4), (6,4), (6,5)
- **Rule 1**: Holes become **8** (azure, the original color)
- **Rule 2**: Body becomes **3** (green, because 3 holes)
- **Rule 3**: Border pixels become **9** (maroon)
- **Result**: Green crown body, azure L inside, maroon outline

### Shape 2: Diamond (original color 3, green)
- **Holes**: 4 holes in square-corner pattern at (4,19), (4,21), (6,19), (6,21)
- **Rule 1**: Holes become **3** (green)
- **Rule 2**: Body becomes **4** (yellow, because 4 holes)
- **Rule 3**: Border → 9
- **Result**: Yellow diamond body, green dots inside, maroon outline

### Shape 3: Shield (original color 6, magenta)
- **Holes**: 1 hole (single dot) at (14,4)
- **Rule 1**: Hole becomes **6** (magenta)
- **Rule 2**: Body becomes **1** (blue, because 1 hole)
- **Rule 3**: Border → 9
- **Result**: Blue shield body, magenta dot inside, maroon outline

### Shape 4: Thick Cross (original color 4, yellow)
- **Holes**: 5 holes in plus pattern at (14,18), (15,17), (15,18), (15,19), (16,18)
- **Rule 1**: Holes become **4** (yellow)
- **Rule 2**: Body becomes **5** (gray, because 5 holes)
- **Rule 3**: Border → 9
- **Result**: Gray cross body, yellow plus inside, maroon outline

### Shape 5: Heart (original color 7, orange)
- **Holes**: 2 holes (vertical pair) at (24,8), (25,8)
- **Rule 1**: Holes become **7** (orange)
- **Rule 2**: Body becomes **2** (red, because 2 holes)
- **Rule 3**: Border → 9
- **Result**: Red heart body, orange pair inside, maroon outline

---

## Walkthrough: Training Example 2

| Shape | Original Color | Holes | New Body Color | Holes Become | Border |
|---|---|---|---|---|---|
| Hexagon | 7 (orange) | 2 | 2 (red) | 7 (orange) | 9 |
| Star | 4 (yellow) | 1 | 1 (blue) | 4 (yellow) | 9 |
| Bell | 8 (azure) | 6 (two rows of 3) | 6 (magenta) | 8 (azure) | 9 |
| Arrow | 3 (green) | 4 (L-shape) | 4 (yellow) | 3 (green) | 9 |
| Goblet | 6 (magenta) | 3 (vertical line) | 3 (green) | 6 (magenta) | 9 |

---

## Walkthrough: Training Example 3

| Shape | Original Color | Holes | New Body Color | Holes Become | Border |
|---|---|---|---|---|---|
| Pentagon | 4 (yellow) | 5 (plus) | 5 (gray) | 4 (yellow) | 9 |
| Mushroom | 3 (green) | 7 (I-beam) | 7 (orange) | 3 (green) | 9 |
| Diamond | 8 (azure) | 1 (dot) | 1 (blue) | 8 (azure) | 9 |
| Crown | 6 (magenta) | 3 (vertical line) | 3 (green) | 6 (magenta) | 9 |
| Heart | 7 (orange) | 4 (square corners) | 4 (yellow) | 7 (orange) | 9 |

---

## Test Example Summary

| Shape | Original Color | Holes | New Body Color | Holes Become | Border |
|---|---|---|---|---|---|
| Hourglass | 4 (yellow) | 3 (horizontal line) | 3 (green) | 4 (yellow) | 9 |
| Shield | 8 (azure) | 2 (vertical pair) | 2 (red) | 8 (azure) | 9 |
| Star | 7 (orange) | 6 (two rows of 3) | 6 (magenta) | 7 (orange) | 9 |
| Goblet | 3 (green) | 5 (plus) | 5 (gray) | 3 (green) | 9 |
| Tree | 6 (magenta) | 4 (square) | 4 (yellow) | 6 (magenta) | 9 |

---

## Difficulty Analysis (10+ Minutes Expected)

1. **Shapes survive into output**: Unlike easy puzzles where shapes vanish and only holes remain, here the full shapes persist — the solver cannot simply compare "what disappeared"

2. **Three-way color change**: Body color, hole color, and border color all change simultaneously. The solver must untangle three interlocking color transformations

3. **Original color is a red herring for body**: The shape's original color has NO bearing on what the body becomes — only the hole count matters. But the original color DOES appear in the holes, creating a confusing feedback loop

4. **Border noise**: The maroon (9) border overwrites edge pixels, hiding the clean recoloring and adding visual complexity

5. **Counting hidden in visual noise**: The solver must isolate which interior pixels are "holes" vs "body" in the input, count them, and then realize that count maps to a color index — all while the output looks like a fully-filled colored shape

6. **15+ training data points needed**: With 5 shapes × 3 training examples = 15 shape transformations to cross-reference, the solver must examine many shapes before the three rules crystallize

---

## Shapes Used Across All Examples

| Shape | Description |
|---|---|
| **Crown** | Three-peaked crown with solid base |
| **Diamond** | Large filled diamond (Manhattan distance radius 5) |
| **Heart** | Classic heart shape tapering to a point |
| **Shield** | Rectangular top tapering to triangular bottom |
| **Cross/Plus** | Thick plus sign |
| **Arrow** | Upward-pointing arrow with shaft |
| **Star** | Six-pointed star with waist |
| **Hexagon** | Wide hexagonal shape |
| **Bell** | Dome top with wide bottom and clapper |
| **Goblet** | Wide top, narrow stem, wide base |
| **Pentagon** | Rounded pentagon dome |
| **Mushroom** | Wide cap with narrow stem |
| **Tree** | Christmas tree with layered tiers |
| **Hourglass** | Wide-narrow-wide symmetric form |

---

## Quality Checklist

- ✅ Grid size: 30×30, consistent across all inputs and outputs
- ✅ No math or arithmetic — only counting (which maps to a color, not a formula)
- ✅ 3 independent interacting rules: inversion + count-to-color + border overlay
- ✅ Shapes are beautiful and fully filled: crowns, diamonds, hearts, stars, shields, hexagons, bells, goblets, pentagons, mushrooms, trees, hourglasses, arrows, crosses
- ✅ All shapes have clear, recognizable forms with no random noise
- ✅ Pattern is novel — body/hole inversion with count-based recoloring and border stamping
- ✅ Output retains full shapes (hard to spot the transformation at a glance)
- ✅ JSON is valid ARC-AGI format with `train` (3 examples × 5 shapes each) and `test` (1 example × 5 shapes)
- ✅ All color values in range 0–9
- ✅ No color conflicts (hole-count color never equals shape's original color)
- ✅ All holes are verified to be strictly interior (not on shape boundary)
- ✅ Hole counts range from 1 to 7 across examples, exercising the full mapping
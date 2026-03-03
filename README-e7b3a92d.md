# Emoji Council Puzzle — `e7b3a92d`

## Overview

A **15×15** grid contains exactly **three emoji faces** arranged in a triangle formation — two on top (left and right) and one at the bottom-center. Each face is either a **Happy Face 🙂** or a **Sad Face ☹️**, drawn as a beautiful 7×7 pixel-art object.

The transformation applies **5 deeply layered rules** based on a **majority vote** among the three faces. The solver must detect face types, determine the majority mood, then apply role-based mouth-flipping, outline recoloring, eye recoloring, and a single-pixel "mood dot" — each governed by a separate conditional rule that interacts with the others.

**Expected solve time: 15–25 minutes.** This puzzle is deliberately TOP-LEVEL HARD because multiple rules produce tiny, easy-to-miss changes (2-pixel eye recoloring, 1-pixel center dot) alongside more visible ones (outline recoloring, mouth flipping).

---

## Color Palette

| Index | Color   | Role in this puzzle                                              |
|-------|---------|------------------------------------------------------------------|
| 0     | Black   | Background                                                       |
| 1     | Blue    | Default eye color (minority faces keep this)                     |
| 2     | Red     | Sad mouth (∩-frown) / Center dot when majority = sad             |
| 3     | Green   | Happy mouth (U-smile) / Center dot when majority = happy         |
| 4     | Yellow  | Default face outline / Outline when unanimous                    |
| 6     | Magenta | Outline of **minority** face (the one that got converted)        |
| 7     | Orange  | Recolored eyes of majority/unanimous faces / Center dot when unanimous happy |
| 8     | Azure   | Outline of **majority** faces (the winners)                      |
| 9     | Maroon  | Center dot when unanimous sad                                    |

---

## Grid Layout — Triangle Formation

All inputs share the same face positions on the 15×15 grid:

```
  ┌───────┐ · ┌───────┐
  │ FACE  │   │ FACE  │     ← Top row: rows 0–6
  │   A   │   │   B   │
  └───────┘   └───────┘
        · · · · ·           ← Row 7: gap (center dot lives here)
      ┌───────┐
      │ FACE  │             ← Bottom row: rows 8–14
      │   C   │
      └───────┘
```

| Face | Top-Left Corner | Rows  | Cols  |
|------|-----------------|-------|-------|
| A    | (0, 0)          | 0–6   | 0–6   |
| B    | (0, 8)          | 0–6   | 8–14  |
| C    | (8, 4)          | 8–14  | 4–10  |

The center of the grid — pixel **(7, 7)** — sits in the gap between the three faces. This is where the mood dot appears.

---

## Face Anatomy (7×7 pixels)

### Happy Face 🙂

```
  · O O O O O ·
  O · · · · · O
  O · E · E · O       ← eyes (E = blue/1 or orange/7)
  O · · · · · O
  O 3 · · · 3 O       ← smile corners raised
  O · 3 3 3 · O       ← smile curve (U-shape, green)
  · O O O O O ·
```

### Sad Face ☹️

```
  · O O O O O ·
  O · · · · · O
  O · E · E · O       ← eyes (E = blue/1 or orange/7)
  O · · · · · O
  O · 2 2 2 · O       ← frown curve (∩-shape, red)
  O 2 · · · 2 O       ← frown corners dropped
  · O O O O O ·
```

`O` = outline color (4, 6, or 8 depending on rules). `E` = eye color (1 or 7).

### The Critical Difference

The **only** structural difference is the mouth region (rows 4–5):

| Row | Happy Interior     | Sad Interior       |
|-----|--------------------|--------------------|
| 4   | `3 · · · 3`       | `· 2 2 2 ·`       |
| 5   | `· 3 3 3 ·`       | `2 · · · 2`       |

The smile is the **vertical mirror** of the frown. Color: green (3) = happy, red (2) = sad.

---

## The Five Rules

### Rule 1 — Majority Vote

Count the three faces by type:

- If **2 or 3** faces are happy → **majority = happy**
- If **2 or 3** faces are sad → **majority = sad**

Special sub-case: if all 3 faces are the **same** type → **unanimous**.

This is the foundational logic that drives all other rules.

---

### Rule 2 — Mouth Contagion (Minority Flips)

The minority face(s) — those not matching the majority — get their mouth **flipped** to match:

- If majority = happy: sad faces flip ∩→U, mouth color red(2)→green(3)
- If majority = sad: happy faces flip U→∩, mouth color green(3)→red(2)

**Result:** All three faces end up with the **same mouth** in the output.

If **unanimous:** No flipping needed — all mouths stay as they are.

---

### Rule 3 — Outline Recoloring by Role

Each face's outline changes based on its role in the vote:

| Scenario          | Face Role    | Outline Change       |
|-------------------|--------------|----------------------|
| Majority exists   | **Majority** (was already the winning type)  | 4 → **8 (azure)**   |
| Majority exists   | **Minority** (got converted)                 | 4 → **6 (magenta)** |
| **Unanimous**     | All faces    | **Stays 4 (yellow)** |

The solver must notice that unanimous = no outline change, while a 2-vs-1 vote produces **two different** outline colors depending on which side each face was on.

---

### Rule 4 — Eye Recoloring (THE SUBTLE RULE)

This is the **hardest rule to spot** — only 2 pixels change per face:

| Scenario          | Face Role    | Eye Color Change     |
|-------------------|--------------|----------------------|
| Majority exists   | **Majority** faces | 1 (blue) → **7 (orange)** |
| Majority exists   | **Minority** face  | **Stays 1 (blue)**        |
| **Unanimous**     | All faces          | 1 (blue) → **7 (orange)** |

**Why this is devious:** The eye change is just 2 pixels per face, buried inside the face interior among many zeros. A solver who only looks at outlines and mouths will miss this entirely, producing a wrong answer that's 95% correct but fails on 4–6 pixels.

---

### Rule 5 — Mood Dot (THE HIDDEN RULE)

A **single pixel** appears at the exact center of the grid — position **(7, 7)** — encoding the overall council verdict:

| Condition            | Dot Color           |
|----------------------|---------------------|
| Majority = happy     | **3 (green)**       |
| Majority = sad       | **2 (red)**         |
| Unanimous happy      | **7 (orange)**      |
| Unanimous sad        | **9 (maroon)**      |

This single pixel sits alone in the gap row between the top and bottom faces. It's the tiniest possible signal — just 1 pixel in a 225-pixel grid — and it carries unique information that distinguishes the "majority" case from the "unanimous" case using a **different color palette**.

---

## Complete Transformation Summary

```
INPUT:  3 faces with yellow(4) outlines, blue(1) eyes, mixed mouths
          ↓
STEP 1: Count happy vs sad → determine majority
STEP 2: Flip minority mouth(s) to match majority
STEP 3: Recolor outlines: majority→azure(8), minority→magenta(6), unanimous→stay(4)
STEP 4: Recolor eyes: majority/unanimous→orange(7), minority→stay blue(1)
STEP 5: Place mood dot at (7,7): happy→green(3), sad→red(2), unan.happy→orange(7), unan.sad→maroon(9)
          ↓
OUTPUT: 3 faces with role-based outlines, recolored eyes, unified mouths, mood dot
```

---

## Training Examples

### Train 0 — 🙂🙂☹️ (Majority Happy)

**Input:** Face A = happy, Face B = happy, Face C = sad.

**Majority:** Happy (2 vs 1).

**Output transformations:**
- Face A (majority): outline 4→**8**, eyes 1→**7**, mouth stays happy ✓
- Face B (majority): outline 4→**8**, eyes 1→**7**, mouth stays happy ✓
- Face C (minority): mouth flips ☹️→🙂, outline 4→**6**, eyes stay **1**
- Mood dot (7,7): **3** (green = happy majority)

**What the solver learns:** The basic majority mechanic, mouth flipping, and outline recoloring. The eye change is present but may not be noticed yet.

---

### Train 1 — ☹️☹️🙂 (Majority Sad)

**Input:** Face A = sad, Face B = sad, Face C = happy.

**Majority:** Sad (2 vs 1).

**Output transformations:**
- Face A (majority): outline→**8**, eyes→**7**, mouth stays sad ✓
- Face B (majority): outline→**8**, eyes→**7**, mouth stays sad ✓
- Face C (minority): mouth flips 🙂→☹️, outline→**6**, eyes stay **1**
- Mood dot: **2** (red = sad majority)

**What the solver learns:** The majority mechanic works for sad too. Happy faces can be forced sad. The center dot color tracks the majority mood. Comparing Trains 0 and 1 reveals the dot color rule (green vs red).

---

### Train 2 — 🙂🙂🙂 (Unanimous Happy)

**Input:** All three faces are happy.

**Majority:** Unanimous happy (3 vs 0).

**Output transformations:**
- All faces: outline **stays 4** (no change!), eyes 1→**7**
- No mouth flipping needed
- Mood dot: **7** (orange = unanimous happy, NOT green!)

**What the solver learns:** This is the critical "break" example. The solver expects outline→8 based on Trains 0–1, but here outlines DON'T change. This reveals the **unanimous exception**: when all agree, no one is "majority" or "minority" — everyone stays yellow. Also, the center dot is **orange (7)** instead of green (3), revealing a 4-way dot color scheme.

---

### Train 3 — ☹️🙂☹️ (Majority Sad, Minority at position B)

**Input:** Face A = sad, Face B = happy, Face C = sad.

**Majority:** Sad (2 vs 1). The minority face is at position **B** (top-right).

**Output transformations:**
- Face A (majority): outline→**8**, eyes→**7**
- Face B (minority): mouth flips 🙂→☹️, outline→**6**, eyes stay **1**
- Face C (majority): outline→**8**, eyes→**7**
- Mood dot: **2** (red)

**What the solver learns:** The minority can be at ANY position — not just the bottom face. Comparing Train 3 with Train 1, the solver sees that the role (majority/minority) depends on face **type**, not **position**. This eliminates position-based hypotheses.

---

## Test Case — 🙂☹️🙂 (Majority Happy, Minority at position B)

**Input:** Face A = happy, Face B = sad, Face C = happy.

**Expected output:**
- Face A (majority): outline→**8**, eyes→**7**, mouth stays happy
- Face B (minority): mouth flips ☹️→🙂, outline→**6**, eyes stay **1**
- Face C (majority): outline→**8**, eyes→**7**, mouth stays happy
- Mood dot: **3** (green)

**Why this is a strong test:**
- The minority face is at position B (top-right), same as Train 3 but with opposite mood
- The solver must apply all 5 rules simultaneously
- Getting the eye colors right requires noticing the subtle Rule 4
- Getting the center dot right requires distinguishing majority(3) from unanimous(7)

---

## What Makes This TOP-LEVEL Hard

### Layer 1: Face Detection (Easy)
The solver must recognize 7×7 faces and distinguish happy from sad by their 2-row mouth pattern. Moderate difficulty — the mouths use different colors (green vs red) and shapes (U vs ∩).

### Layer 2: Majority Logic (Medium)
The solver must count face types and determine majority. This requires comparing all 3 faces, not just pairs. The concept is simple once identified, but extracting it from pixel grids is harder than it sounds.

### Layer 3: Mouth Flipping (Medium)
The minority face's mouth changes to match the majority. This is the most visible transformation and most solvers will notice it first. But they must also realize the mouth *shape* flips (not just the color).

### Layer 4: Outline Recoloring with Unanimous Exception (Hard)
Two different outline colors (azure=majority, magenta=minority) appear in 2-vs-1 cases, but outlines DON'T change in the unanimous case. The solver must process Train 2 carefully to discover this exception. Many solvers will incorrectly apply the majority/minority coloring to Train 2 and get it wrong.

### Layer 5: Eye Recoloring — 2 pixels per face (Very Hard)
This is a **trap for lazy solvers**. The eye change (blue→orange) is just 2 pixels per face. In a 15×15 grid of 225 pixels, that's less than 3% of the grid changing. A solver doing coarse visual comparison may completely miss this. But the rule is consistent: majority/unanimous eyes → orange, minority eyes → stay blue.

### Layer 6: Single-Pixel Mood Dot — 4-way mapping (Extremely Hard)
The center dot at (7,7) is **1 pixel** in a sea of zeros. Its color follows a 4-way conditional:
- Majority happy → green (3)
- Majority sad → red (2)
- Unanimous happy → orange (7)
- Unanimous sad → maroon (9)

The solver must notice this pixel exists, then deduce a mapping that requires distinguishing "majority" from "unanimous" — a distinction only revealed by comparing Train 2 (unanimous, orange dot) against Trains 0/1/3 (majority, green/red dots).

### The Interaction Problem
The real difficulty is that all 5 rules must be discovered and applied **simultaneously**. A solver who gets 4 out of 5 rules will still produce wrong output. The rules interact: the same "majority/minority" logic drives mouth changes, outline colors, eye colors, and dot color — but each uses a **different color mapping**, forcing the solver to track multiple transformation channels in parallel.

---

## Puzzle Categories Used

| Category                        | How it's used                                                            |
|---------------------------------|--------------------------------------------------------------------------|
| **Object Detection**            | Recognizing three 7×7 face objects by structure and mouth pattern        |
| **Pattern Recognition**         | Distinguishing U-smile from ∩-frown (inverted pixel patterns)           |
| **Counting**                    | Counting happy vs sad faces to determine majority (2 vs 1 or 3 vs 0)   |
| **Logic & Conditional Rules**   | 5 if-then rules with majority/minority/unanimous branching              |
| **Color Mapping**               | Four separate recolor channels: outline, eyes, mouth, center dot        |
| **Geometric Transformation**    | Mouth pattern flips vertically (∩ ↔ U) during mood contagion           |
| **Topology**                    | Mood dot placement at the topological center between three face regions |

---

## File Location

```
ARC-AGI-2/data/prince-training/e7b3a92d.json
```

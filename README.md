# 🧩 Gravity Drop Puzzle — `3e9c2a5f`

A custom ARC-AGI puzzle built on three transformation rules applied to colored shapes on a grid.

---

## 📖 Overview

You are given a **16×16 grid** containing **5 colored shapes** floating near the top. Your job is to figure out what the output grid looks like after all three rules are applied — in order.

---

## 🎨 The 5 Shapes

Each input grid contains exactly these five shapes (in varying arrangements):

| Shape | Color Code | Pattern |
|---|---|---|
| 🔴 **Red L-shape** | `2` | `2 · 2` / `2 2 2` / `· 2 ·` |
| 🔵 **Blue P-block** | `1` | `· 1 1` / `1 1 1` / `1 1 ·` |
| 🟢 **Green Corner** | `3` | `· 3` / `3 3` |
| 🟡 **Yellow Small-L** | `4` | `· 4` / `4 4` |
| 🔷 **Cyan Staircase** | `8` | `8 ·` / `8 8` |

---

## 📏 The Three Rules

Apply these rules **in order** to transform the input into the output:

### Rule 1 — Gravity 🪨

> *Sab niche aa jayenge!*

Every non-zero (colored) cell **falls straight down** in its column, as if pulled by gravity. Cells stack at the bottom of the grid, preserving their top-to-bottom order within each column.

**Before:**
```
col:  2 · 2
      · · ·
      2 · 2
      · · ·
      · · ·
```

**After:**
```
col:  · · ·
      · · ·
      · · ·
      2 · 2
      2 · 2
```

---

### Rule 2 — Triple Stack Color Shift 🔺

> *Jab teen same color ka stack banta hai…*

After gravity, look at each **column**. If any column has **3 consecutive cells of the same color**, apply this color shift:

| Position | Change |
|---|---|
| **Top cell** (of the 3) | color **+ 2** |
| **Middle cell** | color **+ 1** |
| **Bottom cell** | **no change** (stays original) |

**Example:** 3 stacked Blue (`1, 1, 1`) becomes → `3, 2, 1` (top to bottom).

**Example:** 3 stacked Green (`3, 3, 3`) becomes → `5, 4, 3` (top to bottom).

> ⚠️ This rule only triggers for exactly 3 consecutive same-color cells in a column.

---

### Rule 3 — Bottom Row Gap Fill 🧱

> *Bottom row mein gaps bhar do!*

Look at the **bottom row only**. If there is a **single-cell gap** (a `0`) between two colored cells, fill it with the **ceiling of the average** of those two neighboring colors.

**Formula:**

```
fill_value = ⌈(left_color + right_color) / 2⌉
```

**Examples:**

| Left Color | Gap | Right Color | Calculation | Fill Value |
|---|---|---|---|---|
| Red (`2`) | `0` | Blue (`1`) | ⌈(2+1)/2⌉ = ⌈1.5⌉ | **2** |
| Blue (`1`) | `0` | Cyan (`8`) | ⌈(1+8)/2⌉ = ⌈4.5⌉ | **5** |
| Cyan (`8`) | `0` | Blue (`1`) | ⌈(8+1)/2⌉ = ⌈4.5⌉ | **5** |
| Green (`3`) | `0` | Cyan (`8`) | ⌈(3+8)/2⌉ = ⌈5.5⌉ | **6** |

> ⚠️ This rule applies **only to the bottom row** and only fills gaps **between** two colored cells.

---

## 🕹️ How to Play

1. **Load the puzzle** — Open the file `data/training/3e9c2a5f.json` in the [ARC testing interface](https://arcprize.org/play) or any compatible viewer.

2. **Study the examples** — The file contains **3 training pairs** (input → output) that demonstrate all three rules in action. Look at them carefully.

3. **Solve the test** — You'll be shown a **test input grid**. Apply the three rules mentally (or on paper) and construct the correct output grid.

4. **Submit** — Build your answer in the grid editor and hit **Submit!**

---

## 🧠 Solving Strategy

Here's a step-by-step approach:

1. **Identify the 5 shapes** and their positions in the input.
2. **Drop everything down** (gravity) — for each column, slide all colored cells to the bottom.
3. **Check each column** for a triple stack of the same color → apply the `+2 / +1 / same` shift.
4. **Scan the bottom row** for single-cell gaps between colored cells → fill with `⌈avg⌉`.
5. That's your output!

---

## 📂 File Location

```
ARC-AGI-2/
└── data/
    ├── 3e9c2a5f.json    ← the puzzle file  
    └── README.md         ← this file
```

---

## 📝 Quick Reference Card

```
┌─────────────────────────────────────────────┐
│  RULE 1: GRAVITY                            │
│  All cells fall to the bottom of their col. │
│                                             │
│  RULE 2: TRIPLE STACK (per column)          │
│  3 same-color cells stacked vertically:     │
│    Top    → color + 2                       │
│    Middle → color + 1                       │
│    Bottom → stays same                      │
│                                             │
│  RULE 3: BOTTOM ROW GAP FILL                │
│  Single gap between two colors:             │
│    Fill = ⌈(left + right) / 2⌉              │
└─────────────────────────────────────────────┘
```

---

*Good luck and have fun! 🎮*
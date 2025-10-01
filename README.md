# 12x8 Matrix Toggle (LED Matrix Editor)

A tiny, dependency‑free HTML app that lets you click (or click‑and‑drag) to toggle a **12 column x 8 row** grid of square cells to generate copy-paste byte array and hex longs for [Arduino LED matrix functions](https://docs.arduino.cc/tutorials/uno-r4-wifi/led-matrix/). Inspired by [LED Matrix Editor](https://ledmatrix-editor.arduino.cc/).

It live‑exports your drawing as:

* An **12x8 list** of comma‑separated **0/1** values (white = 0, black = 1).
* **Three 32‑bit hex longs** representing the 96 bits (row‑major, MSB‑first within each 32‑bit word).
* These values can be one-click copied for use in driving the 12x8 LED matrix on the Arduino UNO R4 or similar.

---

## Quick start

1. Save the provided `index.html` anywhere on your machine.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Click cells to toggle; click‑and‑drag paints contiguous cells.

---

## UI overview

* **Grid:** 12x8 squares. Click or drag to toggle.
* **Clear:** Sets all cells to 0 (white).
* **Invert:** Flips all bits.
* **Copy Array:** Copies the 12x8 0/1 text block (no trailing commas).
* **Copy Hex:** Copies the three 32‑bit words as hex (e.g., `0xE4594594, 0x5F459459, 0x45E77000`).
* **Paste hex → Load:** Paste three 32‑bit hex values (comma/space separated; `0x` optional) and press **Load Hex** to fill the grid.

---

## Data formats

### 12x8 array

Printed exactly as eight lines of **`0, 1, ...`** with spaces after commas and no trailing comma:

```
 1, 0, 0, 1, 0, 0, 0, 1, 1, 0, 0, 0
 0, 1, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0
 ... (8 rows total)
```

### 3 × 32‑bit longs (hex)

* **Row‑major flattening:** index `i = r * 12 + c`.
* **96 bits** are split into 3 groups of 32: `g ∈ {0,1,2}` covers indices `[g*32, g*32+31]`.
* **MSB‑first within each 32‑bit word:** bit `i` of the group is placed at bit position `(31 - (i % 32))`.
* Displayed as uppercase hex with leading zeros: `0xXXXXXXXX`.

---

## Accessibility & UX

* Uses **`role="grid"`/`gridcell`** for assistive tech.
* **Pointer events** enable smooth drag painting on both mouse and touch.
* Visual focus ring on cells for keyboard users.

---

## Browser support

* Requires the standard **Clipboard API** for the copy buttons (supported in modern browsers). If clipboard write is blocked by permissions, the app will show a friendly error.

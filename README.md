# 12x8 Matrix Toggle (LED Matrix Editor)

A tiny, dependency‑free HTML app that lets you click (or click‑and‑drag) to toggle a **12 column x 8 row** grid of square cells to generate copy-paste byte array and hex longs for [Arduino LED matrix functions](https://docs.arduino.cc/tutorials/uno-r4-wifi/led-matrix/). Inspired by [LED Matrix Editor](https://ledmatrix-editor.arduino.cc/).

It live‑exports your drawing as:

* An **[8][12]** array of comma‑separated **0/1** values (white = 0, black = 1).
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
  * Paste into two-dimensional array
    ```
    byte frame[8][12] = {
    <PASTE ARRAY VALUES HERE>
    };
    ```
  * Use as parameter for ```matrix.renderBitmap(frame, 8, 12);```
* **Copy Hex:** Copies the three 32‑bit words as hex (e.g., `0xE4594594, 0x5F459459, 0x45E77000`).
  * Paste into long int array
    ```
    const uint32_t frame[] = {
    <PASTE HEX VALUES HERE>
    };
    ```
  * Use as parameter for ```matrix.loadFrame(frame);```
* **Paste hex → Load:** Paste three 32‑bit hex values (comma/space separated; `0x` optional) and press **Load Hex** to fill the grid.

---

## Example usage
**Goal**: Show the characters "AZ" on the LED matrix.
1. Draw characters on the 12x8 array
2. Click Copy Hex
<img width="600" alt="matrix_AZ" src="https://github.com/user-attachments/assets/426c2b6f-bfb9-498f-a7c8-b500e8882087" />

3. Paste hex values into array variable
```
#include "Arduino_LED_Matrix.h"

ArduinoLEDMatrix matrix;

void setup() {

  matrix.begin();

  const uint32_t AZ[] = {
    0x23E50288,
    0x4888F908,
    0xA08BE000
  };

  matrix.loadFrame(AZ);
}

void loop() {
  // put your main code here, to run repeatedly:

}
```
5. Run code (see [Arduino Docs](https://docs.arduino.cc/tutorials/uno-r4-wifi/led-matrix/#how-to-write-a-frame) for help)
<img width="500" alt="AZ_photo" src="https://github.com/user-attachments/assets/82d8c226-8777-4953-bfd5-ca7fcb523f98" />

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

## 1. CSS Length Units Reference

CSS units specify distance, sizing, typography, and spacing measurements.

### Absolute Units

| Unit | Name | Definition | Common Use Case |
| :--- | :--- | :--- | :--- |
| **`px`** | Pixels | $1\text{px} = 1/96\text{th of an inch}$. | Borders, shadows, fine-tuned component details. |
| **`pt`** | Points | $1\text{pt} = 1/72\text{th of an inch}$. | Print stylesheets (`@media print`). |
| **`in`** / **`cm`** / **`mm`** | Inches, Centimeters, Millimeters | Physical measurements. | Print layouts. |

### Font-Relative Units

| Unit | Relative To | Best Use Case |
| :--- | :--- | :--- |
| **`rem`** | Font size of the root element (`<html>`, typically 16px). | Typography, layout spacing, padding, margins (respects browser user zoom). |
| **`em`** | Font size of the current element (or parent for `font-size`). | Sizing that scales proportionally with the element's text (e.g., button padding, icon sizing). |
| **`ch`** | Width of the "0" glyph of the current font. | Restricting line length for readability (e.g., `max-width: 65ch;`). |
| **`lh`** | Line height of the current element. | Spacing elements relative to text line height. |
| **`cap`** | Capital height of the current font. | Aligning uppercase icons with headlines. |

### Viewport-Relative Units (Including Modern Dynamic Units)

Modern mobile browsers expand and retract address bars and toolbars as users scroll. Dynamic viewport units prevent unwanted page clipping.

| Unit | Full Name | Behavior / Context |
| :--- | :--- | :--- |
| **`vw`** / **`vh`** | Viewport Width / Height | 1% of the viewport width / height. |
| **`vmin`** / **`vmax`** | Viewport Min / Max | 1% of the smaller / larger viewport dimension. |
| **`svw`** / **`svh`** | Small Viewport | Viewport size when mobile browser UI address bars are **visible / expanded**. |
| **`lvw`** / **`lvh`** | Large Viewport | Viewport size when mobile browser UI address bars are **retracted / hidden**. |
| **`dvw`** / **`dvh`** | Dynamic Viewport | **Dynamically updates** in real time as browser chrome expands and contracts. |
| **`vi`** / **`vb`** | Viewport Inline / Block | Logical viewport units corresponding to text flow direction. |

```css
/* Full height mobile screen without being cut off by browser address bar */
.hero-section {
  min-height: 100dvh;
}
```

### Container Query Units

Units sized relative to the nearest ancestor container context (configured via `container-type`).

- **`cqw`** / **`cqh`**: 1% of container width / height.
- **`cqi`** / **`cqb`**: 1% of container inline size / block size.
- **`cqmin`** / **`cqmax`**: 1% of smaller / larger container dimension.

---

## 2. Modern CSS Math Functions

### `calc()`, `min()`, `max()`, and `clamp()`

| Function | Syntax | Description |
| :--- | :--- | :--- |
| **`calc()`** | `calc(expression)` | Evaluates mathematical expressions combining disparate units. |
| **`min()`** | `min(val1, val2, ...)` | Selects the smallest value from a comma-separated list. |
| **`max()`** | `max(val1, val2, ...)` | Selects the largest value from a comma-separated list. |
| **`clamp()`**| `clamp(MIN, PREFERRED, MAX)` | Restricts a value between an allowable lower and upper bound. |

#### Fluid Typography with `clamp()`

`clamp()` enables fluid typography that scales smoothly between mobile and desktop viewports without media queries:

```css
/* Sizing scales from 1.25rem (mobile) up to 2.5rem (desktop) */
h1 {
  font-size: clamp(1.25rem, 1rem + 2.5vw, 2.5rem);
}

/* Fluid container padding */
.container {
  padding-inline: clamp(1rem, 5vw, 4rem);
}
```

### Modern Mathematical Functions (Trig & Rounding)

- **`round(strategy, value, step)`**: Rounds a value according to nearest, up, down, or to-zero.
- **`mod(dividend, divisor)`**: Calculates modulus preserving the divisor sign.
- **`rem(dividend, divisor)`**: Calculates remainder preserving dividend sign.
- **`abs(value)`**: Absolute value.
- **`sign(value)`**: Returns `-1`, `0`, or `1`.

---

## 3. CSS Custom Properties (Variables)

CSS variables allow values to be declared once and reused across stylesheets with full runtime cascading and inheritance.

### Declaring & Consuming Variables

```css
:root {
  /* Global theme tokens */
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --color-text: #0f172a;
  --radius-md: 8px;
  --font-sans: system-ui, -apple-system, sans-serif;
}

.button {
  background-color: var(--color-primary);
  border-radius: var(--radius-md);
  font-family: var(--font-sans);
  
  /* Fallback value provided as second argument */
  padding: var(--btn-padding, 0.75rem 1.5rem);
}

.button:hover {
  background-color: var(--color-primary-hover);
}

/* Local override within specific component or dark mode */
[data-theme="dark"] {
  --color-text: #f8fafc;
  --color-primary: #60a5fa;
}
```

---

## 4. Typed CSS Variables (`@property`)

The `@property` rule defines custom properties with explicit types, inheritance behavior, and initial fallback values. This allows custom properties to be animated and transitioned.

### Syntax Reference

| Descriptor | Values | Purpose |
| :--- | :--- | :--- |
| **`syntax`** | `'<color>'`, `'<length>'`, `'<percentage>'`, `'<angle>'`, `'<number>'`, `'*'` | Specifies the data type required for the property. |
| **`inherits`** | `true`, `false` | Specifies whether the property cascades down child DOM elements. |
| **`initial-value`** | Value matching syntax | Default fallback value. Mandatory if `syntax` is not `'*'`. |

### Smooth Gradient Animation with `@property`

By default, CSS cannot interpolate between gradient stops. Using `@property` makes gradient angles and colors smoothly animatable:

```css
@property --gradient-angle {
  syntax: "<angle>";
  inherits: false;
  initial-value: 0deg;
}

.animated-border {
  --gradient-angle: 0deg;
  background: conic-gradient(from var(--gradient-angle), #ff4545, #00ff99, #006aff, #ff4545);
  animation: spin 3s linear infinite;
}

@keyframes spin {
  to {
    --gradient-angle: 360deg;
  }
}
```

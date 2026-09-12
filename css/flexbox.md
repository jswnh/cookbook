## 1. Flexbox Architecture

Flexible Box Layout (Flexbox) provides a one-dimensional layout model designed for distributing space and aligning items along a primary axis (**main axis**) and secondary axis (**cross axis**).

```
                 Main Axis (row) ------------>
         +--------------------------------------------+
         |  +---------+   +---------+   +---------+   |
Cross    |  | Item 1  |   | Item 2  |   | Item 3  |   |
Axis     |  |         |   |         |   |         |   |
(column) |  +---------+   +---------+   +---------+   |
   |     +--------------------------------------------+
   v
```

---

## 2. Flex Container Properties Reference

Properties applied to the parent container element (`display: flex` or `display: inline-flex`).

| Property | Values | Default | Description |
| :--- | :--- | :--- | :--- |
| **`flex-direction`** | `row`, `row-reverse`, `column`, `column-reverse` | `row` | Establishes the orientation of the main axis. |
| **`flex-wrap`** | `nowrap`, `wrap`, `wrap-reverse` | `nowrap` | Determines whether items must remain on a single line or can wrap onto multiple lines. |
| **`flex-flow`** | `<flex-direction> <flex-wrap>` | `row nowrap` | Shorthand for direction and wrap (e.g., `row wrap`). |
| **`justify-content`**| `start`, `center`, `end`, `space-between`, `space-around`, `space-evenly` | `start` | Distributes free space along the **main axis**. |
| **`align-items`** | `stretch`, `start`, `center`, `end`, `baseline` | `stretch` | Controls item alignment across the **cross axis** for each line. |
| **`align-content`** | `stretch`, `start`, `center`, `end`, `space-between`, `space-around`, `space-evenly` | `normal` | Controls spacing between lines in a multi-line flex container along the cross axis. |
| **`gap`** | Length (e.g. `1rem`, `16px 24px`) | `0` | Defines spacing between adjacent flex items (supports row and column gap). |
| **`row-gap` / `column-gap`** | Length / Percentage | `0` | Specific row or column gutter spacing. |

### Justify Content Values Comparison

- **`start` / `flex-start`**: Items packed toward the start line.
- **`end` / `flex-end`**: Items packed toward the finish line.
- **`center`**: Items centered along the axis.
- **`space-between`**: First item on start line, last on end line, equal space between remaining items.
- **`space-around`**: Equal space on both sides of each item (half-sized gaps at container edges).
- **`space-evenly`**: Equal gutter space between all items and container edges.

---

## 3. Flex Item Properties Reference

Properties applied to direct child elements inside the flex container.

| Property | Accepted Values | Default | Description |
| :--- | :--- | :--- | :--- |
| **`flex`** | Shorthand (`grow shrink basis`) | `0 1 auto` | Combines `flex-grow`, `flex-shrink`, and `flex-basis`. |
| **`flex-grow`** | Positive number ($\ge 0$) | `0` | Ratio of available remaining space the item should absorb. |
| **`flex-shrink`** | Positive number ($\ge 0$) | `1` | Ratio of overflow deficit the item should shrink by when constrained. |
| **`flex-basis`** | Length, %, `auto`, `content` | `auto` | Initial main size of item before remaining space is distributed. |
| **`align-self`** | `auto`, `start`, `center`, `end`, `baseline`, `stretch` | `auto` | Overrides the parent's `align-items` value for this single item. |
| **`order`** | Integer (positive or negative) | `0` | Dictates visual rendering order (lowest number appears first). |

### Common `flex` Shorthand Values

- **`flex: 1`** (`flex: 1 1 0%`): Item grows and shrinks equally to fill all available space from an initial basis of 0.
- **`flex: auto`** (`flex: 1 1 auto`): Item grows and shrinks based on its intrinsic content size.
- **`flex: initial`** (`flex: 0 1 auto`): Item sizes to its content; will not grow, but shrinks if container overflows.
- **`flex: none`** (`flex: 0 0 auto`): Rigid item that neither grows nor shrinks.

---

## 4. Common Practical Flexbox Patterns

### Perfect Centering (Horizontal & Vertical)

```css
.center-box {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 200px;
}
```

### Sticky Footer Layout

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100dvh;
  margin: 0;
}

main {
  flex: 1; /* Expands to fill all remaining vertical viewport space */
}
```

### Navigation Bar with Auto Margins

Using `margin-left: auto` or `margin-inline-start: auto` on a flex child pushes it and all following items to the far edge:

```css
.navbar {
  display: flex;
  align-items: center;
  gap: 1.5rem;
  padding: 1rem 2rem;
}

.brand {
  font-weight: bold;
}

/* Pushes all links after .brand to the far right */
.nav-links {
  margin-left: auto;
  display: flex;
  gap: 1rem;
  list-style: none;
}
```

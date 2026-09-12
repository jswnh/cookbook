## 1. The Box Model

Every element in CSS is represented as a rectangular box comprising content, padding, border, and margin.

### Box Sizing Property

| Property | Values | Description |
| :--- | :--- | :--- |
| **`box-sizing`** | `content-box` (default), `border-box` | Controls how total width and height are calculated. |

```css
/* Universal Modern Box Sizing Reset */
*, *::before, *::after {
  box-sizing: border-box;
}
```

- **`content-box`**: `width` applies only to content. Total element width = `width + padding + border`.
- **`border-box`**: `width` includes content, padding, and borders. Total element width matches declared `width`.

---

## 2. Dimensions & Modern Logical Properties

Logical properties adapt automatically to different writing modes and text directions (e.g., LTR vs RTL).

| Physical Property | Modern Logical Equivalent | Description |
| :--- | :--- | :--- |
| **`width`** | **`inline-size`** | Dimension parallel to text flow (horizontal in English). |
| **`height`** | **`block-size`** | Dimension perpendicular to text flow (vertical in English). |
| **`min-width` / `max-width`** | **`min-inline-size` / `max-inline-size`** | Min/max width constraints. |
| **`min-height` / `max-height`**| **`min-block-size` / `max-block-size`** | Min/max height constraints. |

---

## 3. Spacing: Margins & Padding

### Margin & Padding Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`margin`** | `1-4 values`, `auto` | External clearance space around the border. |
| **`margin-inline`** | `1-2 values`, `auto` | Logical horizontal margin (`margin-inline: auto` centers elements). |
| **`margin-block`** | `1-2 values` | Logical vertical margin (top and bottom). |
| **`padding`** | `1-4 values` | Internal clearance space between content and border. |
| **`padding-inline`** | `1-2 values` | Logical horizontal padding (left and right). |
| **`padding-block`** | `1-2 values` | Logical vertical padding (top and bottom). |

```css
/* Centering a block element with max-width */
.container {
  max-inline-size: 1200px;
  margin-inline: auto;
  padding-inline: 1.5rem;
  padding-block: 2rem;
}
```

> [!NOTE]
> **Margin Collapsing**: Adjacent vertical margins of block elements in standard flow collapse into a single margin equal to the maximum of the two. Horizontal margins and Flex/Grid items never collapse.

---

## 4. Borders, Outlines & Radius

| Property | Example Values | Description |
| :--- | :--- | :--- |
| **`border`** | `1px solid #cbd5e1` | Shorthand for width, style, and color. |
| **`border-width`** | `1px`, `medium`, `thin` | Thickness of border. |
| **`border-style`** | `solid`, `dashed`, `dotted`, `none`, `double` | Stroke style. |
| **`border-color`** | Hex, RGB, OKLCH, `transparent` | Color of border. |
| **`border-radius`** | `8px`, `50%`, `4px 8px 12px 16px` | Curves the corners of elements and backgrounds. |
| **`outline`** | `2px dashed royalblue` | Drawn outside borders; **does not occupy layout space**. |
| **`outline-offset`**| `4px`, `-2px` | Clears space between border and outline (great for focus rings). |

```css
/* Accessible focus visible outline pattern */
button:focus-visible {
  outline: 2px solid #2563eb;
  outline-offset: 3px;
}
```

---

## 5. Overflow & Modern Scrollbars

| Property | Values | Description |
| :--- | :--- | :--- |
| **`overflow`** | `visible`, `hidden`, `clip`, `scroll`, `auto` | Controls content clipping when exceeding container bounds. |
| **`overflow-x` / `overflow-y`** | `hidden`, `auto`, `scroll` | Independent horizontal and vertical overflow rules. |
| **`scrollbar-gutter`** | `auto`, `stable`, `stable both-edges` | **Prevents layout shifts** by permanently reserving space for the scrollbar. |
| **`scrollbar-width`** | `auto`, `thin`, `none` | Controls scrollbar thickness across all modern engines. |
| **`scrollbar-color`** | `thumb-color track-color` | Styles scrollbar thumb and track colors. |
| **`overscroll-behavior`**| `auto`, `contain`, `none` | Prevents scroll chaining and mobile pull-to-refresh. |

```css
/* Clean modern scrollable container */
.scroll-panel {
  overflow-y: auto;
  scrollbar-gutter: stable;
  scrollbar-width: thin;
  scrollbar-color: #94a3b8 #f1f5f9;
  overscroll-behavior: contain;
}
```

---

## 6. Display Modes

| Property Value | Behavior |
| :--- | :--- |
| **`display: block`** | Starts on a new line; stretches to full container width. |
| **`display: inline`** | Renders inline within text flow; ignores vertical width/height. |
| **`display: inline-block`**| Formats inline with text, but respects width, height, and margins. |
| **`display: none`** | Removes element completely from visual layout and accessibility tree. |
| **`display: contents`** | Discards the container box; children render as if they were direct children of the parent. |
| **`display: flow-root`** | Establishes a new block formatting context (replaces old clearfix hacks). |
| **`display: flex`** | Formats direct children using one-dimensional Flexbox layout. |
| **`display: grid`** | Formats direct children using two-dimensional Grid layout. |

---

## 7. Positioning & Stacking Context

### Positioning Types (`position`)

| Type | Reference Coordinates | Stays in Flow? |
| :--- | :--- | :--- |
| **`static`** (default) | Normal document flow. Inset properties ignored. | Yes |
| **`relative`** | Relative to its normal position in flow. | Yes (space is reserved) |
| **`absolute`** | Relative to nearest non-static positioned ancestor. | No (removed from flow) |
| **`fixed`** | Relative to the viewport. | No (remains pinned during scroll) |
| **`sticky`** | Toggles between relative and fixed based on scroll position. | Yes (until scroll threshold met) |

### Inset Shorthand

The **`inset`** property is the modern shorthand replacing `top`, `right`, `bottom`, and `left`.

```css
/* Full overlay cover */
.modal-overlay {
  position: fixed;
  inset: 0; /* top: 0; right: 0; bottom: 0; left: 0; */
  background: rgba(0, 0, 0, 0.5);
  z-index: 100;
}

/* Sticky header */
header.site-header {
  position: sticky;
  top: 0;
  z-index: 50;
}
```

### Stacking Context & Isolation

- **`z-index`**: Controls stacking order along the Z-axis for positioned elements. Requires an explicit integer value (higher values stack above lower values).
- **`isolation: isolate`**: Creates a brand new stacking context without requiring `z-index` or `position: relative`. Prevents child z-indexes from leaking into global layout layers.

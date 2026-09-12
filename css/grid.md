## 1. CSS Grid Architecture

CSS Grid Layout is a two-dimensional layout system that handles both columns (horizontal tracks) and rows (vertical tracks) simultaneously.

```
       Column 1        Column 2        Column 3
    +-------------+ +-------------+ +-------------+
Row |             | |             | |             |
 1  |  Cell (1,1) | |  Cell (1,2) | |  Cell (1,3) |
    +-------------+ +-------------+ +-------------+
    <------------- Gap ----------->
Row |             | |             | |             |
 2  |  Cell (2,1) | |  Cell (2,2) | |  Cell (2,3) |
    +-------------+ +-------------+ +-------------+
```

---

## 2. Grid Container Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`display`** | `grid`, `inline-grid` | Defines the element as a grid container. |
| **`grid-template-columns`** | Track sizes, `fr`, `repeat()`, `minmax()` | Defines line names and track size functions for columns. |
| **`grid-template-rows`** | Track sizes, `fr`, `repeat()`, `minmax()` | Defines line names and track size functions for rows. |
| **`grid-template-areas`** | Strings of named areas (e.g., `"header header"`) | Defines a grid layout using named template areas. |
| **`grid-auto-columns`** | Track sizing | Specifies size for implicitly created column tracks. |
| **`grid-auto-rows`** | Track sizing | Specifies size for implicitly created row tracks. |
| **`grid-auto-flow`** | `row`, `column`, `dense`, `row dense` | Controls how auto-placed items are inserted into the grid. |
| **`gap`** | `row-gap column-gap` | Gutter spacing between rows and columns. |
| **`justify-items`** | `start`, `center`, `end`, `stretch` | Aligns all grid items along the inline (row) axis. |
| **`align-items`** | `start`, `center`, `end`, `stretch` | Aligns all grid items along the block (column) axis. |
| **`place-items`** | `<align-items> <justify-items>` | Shorthand for `align-items` and `justify-items`. |
| **`justify-content`** | `start`, `center`, `end`, `space-between`, `space-evenly` | Distributes entire grid tracks along inline axis. |
| **`align-content`** | `start`, `center`, `end`, `space-between`, `space-evenly` | Distributes entire grid tracks along block axis. |
| **`place-content`** | `<align-content> <justify-content>` | Shorthand for `align-content` and `justify-content`. |

---

## 3. Grid Item Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`grid-column-start` / `grid-column-end`** | Line number, `span N`, `name` | Starting and ending column lines for an item. |
| **`grid-column`** | `<start> / <end>` (e.g. `1 / -1`, `span 2`) | Shorthand for column start and end. |
| **`grid-row-start` / `grid-row-end`** | Line number, `span N`, `name` | Starting and ending row lines for an item. |
| **`grid-row`** | `<start> / <end>` | Shorthand for row start and end. |
| **`grid-area`** | Area name or `<row-start>/<col-start>/<row-end>/<col-end>` | Assigns item to a named area or coordinates. |
| **`justify-self`** | `start`, `center`, `end`, `stretch` | Overrides inline alignment for a single item. |
| **`align-self`** | `start`, `center`, `end`, `stretch` | Overrides block alignment for a single item. |
| **`place-self`** | `<align-self> <justify-self>` | Shorthand for `align-self` and `justify-self`. |

---

## 4. Subgrid (CSS Grid Level 2)

Subgrid allows a grid item that is also a grid container to inherit track definitions (columns or rows) directly from its parent grid.

### The Problem Subgrid Solves

In standard nested grids, child cards cannot align their internal headers, paragraphs, or buttons with adjacent cards because each card calculates its own independent tracks. `subgrid` shares the parent tracks across all children.

```css
/* Parent Grid */
.cards-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}

/* Child Card using Subgrid on Rows */
.card {
  display: grid;
  grid-row: span 3; /* Spans 3 tracks from parent */
  grid-template-rows: subgrid; /* Inherits row sizing directly from parent */
  gap: 0.5rem;
}
```

```html
<div class="cards-container">
  <article class="card">
    <h3>Card Title 1</h3>
    <p>Short description.</p>
    <button>Action</button>
  </article>

  <article class="card">
    <h3>Much Longer Multi-line Card Title 2</h3>
    <p>Longer body text describing more features in detail.</p>
    <button>Action</button>
  </article>
</div>
```

All titles, body paragraphs, and action buttons will stay perfectly horizontally aligned across all cards regardless of text length!

---

## 5. Practical Grid Layout Patterns

### Responsive Auto-Fit Grid (Zero Media Queries)

Automatically wraps columns based on available space:

```css
.responsive-card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}
```

- **`auto-fit`**: Stretches existing items to fill extra space.
- **`auto-fill`**: Reserves empty tracks for potential future items rather than expanding existing ones.

### Holy Grail Page Layout with Named Areas

```css
.page-layout {
  display: grid;
  min-height: 100dvh;
  grid-template-rows: auto 1fr auto;
  grid-template-columns: 240px 1fr 200px;
  grid-template-areas:
    "header header  header"
    "nav    main    aside"
    "footer footer  footer";
  gap: 1rem;
}

header { grid-area: header; }
nav    { grid-area: nav; }
main   { grid-area: main; }
aside  { grid-area: aside; }
footer { grid-area: footer; }

/* Responsive reflow on smaller screens */
@media (max-width: 768px) {
  .page-layout {
    grid-template-columns: 1fr;
    grid-template-areas:
      "header"
      "nav"
      "main"
      "aside"
      "footer";
  }
}
```

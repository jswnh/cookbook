# DOM Styling & Layout

A comprehensive guide to managing CSS classes, inline styles, CSS custom properties, computed styles, layout geometry, coordinates, and high-performance layout patterns.

---

## 1. CSS Class Manipulation (`classList`)

The `element.classList` API provides a convenient `DOMTokenList` interface for manipulating CSS class names without parsing `element.className` manually.

| Method | Description | Example |
| :--- | :--- | :--- |
| `add(...tokens)` | Adds one or more classes | `el.classList.add("btn", "btn-primary")` |
| `remove(...tokens)` | Removes one or more classes | `el.classList.remove("loading", "disabled")` |
| `toggle(token, force?)` | Toggles class; optional `force` boolean | `el.classList.toggle("dark-mode", isDark)` |
| `contains(token)` | Returns `true` if class is present | `if (el.classList.contains("active"))` |
| `replace(old, new)` | Replaces old class with new class | `el.classList.replace("theme-light", "theme-dark")` |

```javascript
const modal = document.querySelector("#settings-modal");

// Add and remove multiple classes at once
modal.classList.add("fade-in", "visible");
modal.classList.remove("hidden", "loading");

// Conditional toggle using the boolean force parameter
const hasUnsavedChanges = true;
modal.classList.toggle("has-changes", hasUnsavedChanges);

// Safe replacement
modal.classList.replace("status-pending", "status-approved");
```

---

## 2. Inline Styles & CSS Variables

### Standard Inline Styles

```javascript
const banner = document.querySelector(".banner");

// 1. Direct camelCase property access
banner.style.backgroundColor = "#2563eb";
banner.style.fontSize = "1.25rem";
banner.style.borderRadius = "8px";

// 2. Clear an inline style property
banner.style.backgroundColor = ""; // Resets to stylesheet value
```

### Modern `setProperty()`, `getPropertyValue()` & CSS Variables
`setProperty` is required for kebab-case names and CSS custom properties (`--var`), and supports `!important`.

```javascript
const root = document.documentElement; // <html> element

// Set a CSS Custom Property (CSS Variable)
root.style.setProperty("--primary-color", "#10b981");
root.style.setProperty("--header-height", "64px");

// Set inline style with '!important'
banner.style.setProperty("display", "flex", "important");

// Read inline property value
const currentHeaderHeight = root.style.getPropertyValue("--header-height");

// Remove property
banner.style.removeProperty("display");
```

---

## 3. Computed Styles (`window.getComputedStyle`)

`element.style` only reads **inline styles** declared on the element itself. To read the final resolved CSS values applied by stylesheets and browser defaults, use `window.getComputedStyle()`.

```javascript
const button = document.querySelector(".btn-submit");

// Get all computed styles
const styles = window.getComputedStyle(button);

// Read resolved pixel values (computed styles always resolve to px or absolute values)
console.log(styles.width);           // e.g. "140px"
console.log(styles.backgroundColor); // e.g. "rgb(37, 99, 235)"
console.log(styles.display);         // e.g. "inline-flex"

// Read CSS variables defined in stylesheets
const brandColor = styles.getPropertyValue("--primary-color").trim();

// Inspect pseudo-elements like ::before or ::after
const afterStyles = window.getComputedStyle(button, "::after");
console.log(afterStyles.content); // e.g. '"→"'
```

---

## 4. Layout Geometry & Dimensions

Understanding DOM dimension properties is critical for tooltips, dropdowns, sticky headers, and virtualization.

```
+-------------------------------------------------------------+
| Margin                                                      |
|   +-------------------------------------------------------+ |
|   | Border                                                | |
|   |   +-------------------------------------------------+ | |
|   |   | Padding                                         | | |
|   |   |   +-------------------------------------------+ | | |
|   |   |   | Content                                   | | | |
|   |   |   | (clientWidth / clientHeight = Pad + Cont) | | | |
|   |   |   +-------------------------------------------+ | | |
|   |   +-------------------------------------------------+ | |
|   |   (offsetWidth / offsetHeight = Bord + Pad + Cont)    | |
|   +-------------------------------------------------------+ |
+-------------------------------------------------------------+
```

### Dimensions Comparison Table

| Metric | What is Included | Excluded |
| :--- | :--- | :--- |
| **`offsetWidth` / `offsetHeight`** | Content + Padding + Borders + Scrollbar | Margins |
| **`clientWidth` / `clientHeight`** | Content + Padding | Borders, Margins, Scrollbar |
| **`scrollWidth` / `scrollHeight`** | Total scrollable content + Padding | Borders, Margins |
| **`clientTop` / `clientLeft`** | Top / Left Border width | Content, Padding |
| **`offsetTop` / `offsetLeft`** | Distance from nearest positioned `offsetParent` | - |

---

## 5. Viewport Coordinates (`getBoundingClientRect`)

`element.getBoundingClientRect()` returns a `DOMRect` object containing the element's size and position relative to the **current visible viewport** with subpixel precision.

```javascript
const tooltipTarget = document.querySelector("#info-badge");
const rect = tooltipTarget.getBoundingClientRect();

console.log(rect.width);   // Rendered width (including padding & borders)
console.log(rect.height);  // Rendered height
console.log(rect.top);     // Distance from top of visible viewport
console.log(rect.bottom);  // Distance from top of visible viewport to element bottom
console.log(rect.left);    // Distance from left of visible viewport
console.log(rect.right);   // Distance from left of visible viewport to element right

// Calculate absolute position relative to entire document (including scroll):
const absoluteTop = rect.top + window.scrollY;
const absoluteLeft = rect.left + window.scrollX;
```

---

## 6. Programmatic & Smooth Scrolling

### Smooth Scrolling to an Element (`scrollIntoView`)

```javascript
const targetSection = document.querySelector("#features-section");

targetSection.scrollIntoView({
  behavior: "smooth", // 'smooth' | 'instant' | 'auto'
  block: "start",     // 'start' | 'center' | 'end' | 'nearest'
  inline: "nearest"
});
```

### Scrolling the Window or a Container

```javascript
// Smoothly scroll back to the top of the page
window.scrollTo({
  top: 0,
  behavior: "smooth"
});

// Scroll a container relative to its current scroll position
const chatWindow = document.querySelector(".chat-messages");
chatWindow.scrollBy({
  top: 200,
  behavior: "smooth"
});

// Jump directly to the bottom of a chat log
chatWindow.scrollTop = chatWindow.scrollHeight;
```

---

## 7. Performance: Avoiding Layout Thrashing

**Layout Thrashing** (forced synchronous reflow) occurs when JavaScript alternates repeatedly between **reading** geometric properties (which forces the browser to recalculate layout) and **writing** DOM changes.

### Bad: Forced Synchronous Reflow Loop

```javascript
// BAD: Alternating read and write forces layout recalculation on every iteration!
const cards = document.querySelectorAll(".card");
cards.forEach((card) => {
  const currentHeight = card.offsetHeight; // READ (forces reflow)
  card.style.height = `${currentHeight + 10}px`; // WRITE (invalidates layout)
});
```

### Good: Batch Reads First, Then Writes

```javascript
// GOOD: Read all dimensions first, then apply mutations in a single batch
const cards = document.querySelectorAll(".card");

// Phase 1: Read all measurements
const heights = [...cards].map((card) => card.offsetHeight);

// Phase 2: Apply writes together
cards.forEach((card, index) => {
  card.style.height = `${heights[index] + 10}px`;
});
```

### Scheduling Visual Updates with `requestAnimationFrame`

```javascript
function updateProgressBar(percentage) {
  // Schedules DOM update right before the next browser paint (60fps/120fps sync)
  requestAnimationFrame(() => {
    progressBar.style.width = `${percentage}%`;
  });
}
```

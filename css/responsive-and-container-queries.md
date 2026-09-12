## 1. Modern Media Queries (`@media`)

Media queries apply styles based on device characteristics, screen dimensions, and user accessibility preferences.

### Modern Range Syntax (Baseline)

Modern CSS supports mathematical comparison operators (`>=`, `<=`, `>`, `<`), replacing verbose `min-width` and `max-width` syntax.

| Old Syntax | Modern Range Syntax |
| :--- | :--- |
| `@media (min-width: 768px)` | `@media (width >= 768px)` |
| `@media (max-width: 640px)` | `@media (width <= 640px)` |
| `@media (min-width: 600px) and (max-width: 900px)` | `@media (600px <= width <= 900px)` |

```css
/* Mobile styles first (default) */
.content-grid {
  grid-template-columns: 1fr;
}

/* Tablet & Desktop */
@media (width >= 768px) {
  .content-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (width >= 1200px) {
  .content-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

---

## 2. User Preferences & Capability Queries

Modern responsive design adapts not just to screen dimensions, but also to user physical needs and device input modes.

### Accessibility Preference Queries

| Feature | Values | Description |
| :--- | :--- | :--- |
| **`prefers-color-scheme`** | `dark`, `light` | Detects user system dark/light theme preference. |
| **`prefers-reduced-motion`**| `reduce`, `no-preference`| Detects whether user has requested minimal motion/animation. |
| **`prefers-contrast`** | `more`, `less`, `custom` | Adapts contrast ratios for users with visual impairments. |

```css
/* Respect user animation preference */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Dark theme overrides */
@media (prefers-color-scheme: dark) {
  :root {
    --bg-surface: #0f172a;
    --text-color: #f8fafc;
  }
}
```

### Input Capability Queries

| Feature | Values | Meaning |
| :--- | :--- | :--- |
| **`hover`** | `hover`, `none` | Primary input can hover over elements (mouse vs touch screen). |
| **`pointer`** | `fine`, `coarse`, `none` | Accuracy of pointing device (`fine` = mouse/stylus; `coarse` = touch/finger). |

```css
/* Enlarges tap targets specifically for touch screens */
@media (pointer: coarse) {
  .nav-item {
    min-height: 48px;
    padding: 12px 16px;
  }
}
```

---

## 3. Container Queries (`@container`)

Container queries allow a component to style itself based on the size of its **immediate parent container** rather than the global viewport. This makes components truly portable across sidebars, modal windows, and wide main columns.

### Defining a Container Context

| Property | Values | Description |
| :--- | :--- | :--- |
| **`container-type`** | `inline-size`, `size`, `normal` | Establishes the element as a query container (`inline-size` tracks width). |
| **`container-name`** | Custom identifier | Assigns an optional name to disambiguate nested containers. |
| **`container`** | `<name> / <type>` | Shorthand for name and type. |

### Container Query Example

```css
/* 1. Designate the parent wrapper as a container */
.card-wrapper {
  container-type: inline-size;
  container-name: product-card;
}

/* 2. Default styles when container is small (e.g., in a narrow sidebar) */
.product {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

/* 3. Reflow when the container itself has at least 450px of width */
@container product-card (inline-size >= 450px) {
  .product {
    flex-direction: row;
    align-items: center;
  }

  .product-img {
    width: 140px;
    height: 140px;
  }
}
```

---

## 4. Responsive Media & Utility Properties

| Property | Values | Description |
| :--- | :--- | :--- |
| **`aspect-ratio`** | Ratio (e.g., `16 / 9`, `1 / 1`, `4 / 3`) | Enforces an explicit aspect ratio regardless of dimensions (replaces padding hacks). |
| **`object-fit`** | `cover`, `contain`, `fill`, `none`, `scale-down` | Defines how `<img>` or `<video>` content fits inside its box. |
| **`object-position`**| `center`, `top left`, `50% 25%` | Determines coordinate alignment of replaced element content within its box. |

```css
/* Responsive video or card thumbnail */
.thumbnail-media {
  width: 100%;
  aspect-ratio: 16 / 9;
  object-fit: cover;
  object-position: center;
  border-radius: 8px;
}
```

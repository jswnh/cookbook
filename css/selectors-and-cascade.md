## 1. Basic Selectors & Combinators

CSS selectors identify the HTML elements to which a set of CSS rules apply.

### Basic Selectors

| Selector | Syntax | Description | Example |
| :--- | :--- | :--- | :--- |
| **Universal** | `*` | Matches every element in the document. | `* { box-sizing: border-box; }` |
| **Type / Tag** | `element` | Matches all elements with the specified HTML tag name. | `p { line-height: 1.6; }` |
| **Class** | `.class` | Matches elements whose `class` attribute contains the name. | `.btn { cursor: pointer; }` |
| **ID** | `#id` | Matches the single unique element with the specified `id`. | `#main-header { position: sticky; }` |

### Attribute Selectors

| Selector | Matches Elements Where Attribute... | Example |
| :--- | :--- | :--- |
| **`[attr]`** | Has the specified attribute present. | `input[required]` |
| **`[attr="val"]`** | Exactly matches `"val"`. | `input[type="checkbox"]` |
| **`[attr^="val"]`** | Value starts with `"val"`. | `a[href^="https://"]` |
| **`[attr$="val"]`** | Value ends with `"val"`. | `a[href$=".pdf"]` |
| **`[attr*="val"]`** | Value contains the substring `"val"`. | `img[src*="thumbnail"]` |
| **`[attr~="val"]`** | Value is a whitespace-separated list containing `"val"`. | `[data-tags~="featured"]` |
| **`[attr\|="val"]`**| Value is `"val"` or starts with `"val-"`. | `[lang\|="en"]` |
| **Modifier `i` / `s`** | Case-insensitive (`i`) or case-sensitive (`s`) matching. | `a[href$=".pdf" i]` |

### Combinators

| Combinator | Syntax | Relationship | Description |
| :--- | :--- | :--- | :--- |
| **Descendant** | `A B` | Any descendant | Matches `B` nested anywhere inside `A`. |
| **Child** | `A > B` | Direct child | Matches `B` that is an immediate direct child of `A`. |
| **Next-Sibling** | `A + B` | Adjacent sibling | Matches `B` immediately preceded by `A` (sharing same parent). |
| **Subsequent-Sibling** | `A ~ B` | General sibling | Matches `B` preceded by `A` anywhere within the same parent. |

---

## 2. Pseudo-Classes

Pseudo-classes select elements based on state, position, or relationships.

### User Action & Focus Pseudo-Classes

| Pseudo-Class | Trigger Condition |
| :--- | :--- |
| **`:hover`** | User points mouse or cursor over the element. |
| **`:active`** | Element is being activated by user (e.g. mouse button pressed down). |
| **`:focus`** | Element currently has input focus (mouse or keyboard). |
| **`:focus-visible`**| Element has focus AND the user agent determines visible ring is needed (e.g., keyboard tab). |
| **`:focus-within`** | The element OR any of its descendants has focus. |

### Form Validation Pseudo-Classes

| Pseudo-Class | Trigger Condition |
| :--- | :--- |
| **`:checked`** | Radio button, checkbox, or option is toggled on. |
| **`:disabled`** / **`:enabled`** | Element is disabled or active for user interaction. |
| **`:required`** / **`:optional`** | Input has the `required` attribute or not. |
| **`:valid`** / **`:invalid`** | Input passes or fails constraint validation rules. |
| **`:user-valid`** / **`:user-invalid`** | (Modern) Triggers validation styles ONLY after the user has interacted with the field. |

### Tree-Structural & Nth-Child Pseudo-Classes

```css
/* First and last elements */
li:first-child { font-weight: bold; }
li:last-child { border-bottom: none; }
li:only-child { padding: 1rem; }

/* Odd and even striping */
tr:nth-child(even) { background-color: #f9f9f9; }
tr:nth-child(odd) { background-color: #ffffff; }

/* Mathematical formula: 3n + 1 (1st, 4th, 7th...) */
li:nth-child(3n + 1) { color: dodgerblue; }

/* Modern Nth-Child of Selector (Baseline) */
/* Selects the 2nd item that has the .highlight class */
li:nth-child(2 of .highlight) {
  border: 2px solid gold;
}
```

### Relational & Logical Selectors (`:has`, `:is`, `:where`, `:not`)

| Selector | Specificity Behavior | Purpose |
| :--- | :--- | :--- |
| **`:has()`** | Specificity of its most specific argument | **Parent / Relational selector**: matches an element if its arguments match descendants or siblings. |
| **`:is()`** | Takes the specificity of its most specific argument | Groups selector lists to reduce duplication. |
| **`:where()`**| Always **zero** specificity (`0, 0, 0`) | Groups selector lists with zero specificity (ideal for resets/frameworks). |
| **`:not()`** | Specificity of its argument | Negates selectors, matching elements that do not match the argument. |

#### Modern `:has()` Parent Selector Examples

```css
/* Styles a card ONLY if it contains an image */
.card:has(img) {
  display: grid;
  grid-template-columns: 120px 1fr;
}

/* Styles a form label when the following input is invalid */
label:has(+ input:invalid) {
  color: crimson;
}

/* Freezes body scrolling when a native dialog is open */
body:has(dialog[open]) {
  overflow: hidden;
}
```

---

## 3. Pseudo-Elements

Pseudo-elements style specific parts of an element or generate cosmetic content.

| Pseudo-Element | Purpose | Example |
| :--- | :--- | :--- |
| **`::before`** | Inserts cosmetic content before element content. | `.badge::before { content: "• "; }` |
| **`::after`** | Inserts cosmetic content after element content. | `.link::after { content: " ↗"; }` |
| **`::marker`** | Styles the bullet point or number of list items. | `li::marker { color: royalblue; }` |
| **`::selection`**| Styles user-highlighted text selection. | `::selection { background: #334155; color: #fff; }` |
| **`::placeholder`**| Styles placeholder text in inputs and textareas. | `input::placeholder { color: #94a3b8; }` |
| **`::backdrop`**| Styles the background layer behind a modal `<dialog>` or popover. | `dialog::backdrop { background: rgba(0,0,0,0.6); }` |
| **`::file-selector-button`**| Styles the button inside `<input type="file">`. | `input::file-selector-button { background: #e2e8f0; }` |

---

## 4. Specificity & Cascade Layers (`@layer`)

### Specificity Hierarchy

Specificity determines which rule wins when multiple rules target the same element:

$$\text{Inline Styles} > \text{IDs} > \text{Classes / Attributes / Pseudo-classes} > \text{Elements / Pseudo-elements}$$

| Level | Weight | Examples |
| :--- | :--- | :--- |
| **Inline style** | `1, 0, 0, 0` | `<div style="color: red;">` |
| **ID** | `0, 1, 0, 0` | `#header`, `#nav` |
| **Class, Attribute, Pseudo-class** | `0, 0, 1, 0` | `.btn`, `[type="text"]`, `:hover`, `:has()` |
| **Element, Pseudo-element** | `0, 0, 0, 1` | `h1`, `p`, `::before` |

> [!NOTE]
> `!important` overrides normal specificity entirely. Use sparingly to prevent unmaintainable style overrides.

### Cascade Layers (`@layer`)

Cascade layers allow developers to control specificity order across resets, frameworks, components, and utilities. **Later layers always override earlier layers**, regardless of selector specificity inside the layer. Unlayered styles always take highest priority.

```css
/* Explicit layer order definition */
@layer reset, base, components, utilities;

@layer reset {
  * { margin: 0; padding: 0; box-sizing: border-box; }
}

@layer components {
  /* High specificity inside components layer */
  .card .card-title {
    color: #1e293b;
    font-size: 1.25rem;
  }
}

@layer utilities {
  /* Lower specificity in later layer WINS without needing !important */
  .text-danger {
    color: crimson;
  }
}
```

---

## 5. Native CSS Nesting

Modern CSS supports native nesting without requiring Sass or preprocessors. The ampersand (`&`) refers to the parent selector.

```css
.card {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 1.5rem;

  /* Nested element selector */
  h2 {
    font-size: 1.5rem;
    color: #0f172a;
  }

  /* Direct child nesting */
  > p {
    color: #475569;
  }

  /* Pseudo-class on the parent */
  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }

  /* Modifiers on the parent */
  &.is-active {
    border: 2px solid dodgerblue;
  }

  /* Nested media query */
  @media (max-width: 640px) {
    padding: 1rem;
  }
}
```

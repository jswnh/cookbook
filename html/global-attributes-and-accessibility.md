## 1. Global Attributes Reference

Global attributes are attributes that can be used on **any** valid HTML element.

| Attribute | Accepted Values | Description |
| :--- | :--- | :--- |
| **`id`** | Unique alphanumeric string | Unique document-wide identifier used by CSS, JS, and fragment links. |
| **`class`** | Space-separated tokens | Assigns one or more CSS class names to the element. |
| **`style`** | Inline CSS declaration | Applies inline style rules directly to the element. |
| **`title`** | Plain text string | Provides advisory tooltip information shown on desktop hover. |
| **`lang`** | BCP 47 language tag | Declares primary human language (e.g., `en-US`, `es-MX`, `ja`). |
| **`dir`** | `ltr`, `rtl`, `auto` | Directionality of the element's text. |
| **`hidden`** | Boolean or `"until-found"` | Hides element from display. `hidden="until-found"` allows browser in-page search (Ctrl+F) to reveal the element automatically. |
| **`inert`** | Boolean attribute | Disables user interaction, mouse clicks, keyboard focus, and screen reader access for the entire DOM subtree. |
| **`tabindex`** | `0`, `-1` | Controls keyboard navigation: `0` places in sequential tab order; `-1` makes it focusable only via script (`el.focus()`). |
| **`contenteditable`**| `"true"`, `"false"`, `"plaintext-only"` | Allows users to edit the rendered contents directly in the page. |
| **`spellcheck`** | `"true"`, `"false"` | Hints whether browser spell-check should be active. |
| **`draggable`** | `"true"`, `"false"` | Specifies whether the element can be dragged using the Drag and Drop API. |
| **`data-*`** | Arbitrary string | Stores custom client data accessible via JavaScript `element.dataset`. |

### Inert Subtree Example

The `inert` attribute is invaluable when displaying modal sheets or background layers, guaranteeing that keyboard focus cannot escape into inactive portions of the page.

```html
<!-- When modal is open, inert freezes all background interactions -->
<div id="main-content" inert>
  <button>Cannot be clicked or focused</button>
  <input type="text" placeholder="Cannot be typed into">
</div>
```

### Custom Data Attributes Example

```html
<article 
  class="product-card" 
  data-product-id="9821" 
  data-category="electronics" 
  data-in-stock="true"
>
  <h3>Wireless Headphones</h3>
</article>

<script>
  const card = document.querySelector(".product-card");
  console.log(card.dataset.productId); // "9821"
  console.log(card.dataset.inStock);   // "true"
</script>
```

---

## 2. Web Accessibility & ARIA Core

ARIA (Accessible Rich Internet Applications) attributes complement HTML when native semantics are insufficient.

> [!IMPORTANT]
> **First Rule of ARIA Use**: If you can use a native HTML element (e.g., `<button>`, `<dialog>`, `<nav>`) with the semantics and behavior already built in, do so instead of re-purposing an element and adding ARIA.

### Essential ARIA Attributes Reference

| Attribute | Accepted Values | Purpose |
| :--- | :--- | :--- |
| **`aria-label`** | String | Provides an invisible accessible name for elements without visible text (e.g., icon-only buttons). |
| **`aria-labelledby`** | Element `id` or list of IDs | Points to an element containing the visible text label. |
| **`aria-describedby`**| Element `id` or list of IDs | Points to secondary descriptive text (e.g., helper text, error messages). |
| **`aria-hidden`** | `"true"`, `"false"` | Hides decorative elements (icons, visual separators) from screen readers entirely. |
| **`aria-expanded`** | `"true"`, `"false"` | Informs assistive technology whether a collapsible section is open or closed. |
| **`aria-controls`** | Element `id` | Identifies the element whose contents or presence is controlled by this control. |
| **`aria-live`** | `"off"`, `"polite"`, `"assertive"` | Announces dynamic content updates: `polite` waits until idle; `assertive` interrupts immediately. |
| **`aria-invalid`** | `"true"`, `"false"`, `"grammar"`, `"spelling"` | Indicates that the entered value does not conform to expected format. |

### Accessible Icon Button Pattern

```html
<!-- Icon button with accessible label and hidden SVG icon -->
<button type="button" aria-label="Close settings panel">
  <svg aria-hidden="true" width="16" height="16" viewBox="0 0 16 16">
    <path d="M1 1l14 14M15 1L1 15" stroke="currentColor" stroke-width="2"/>
  </svg>
</button>
```

### Dynamic Live Region Pattern

```html
<!-- Informs screen reader whenever a notification or status update arrives -->
<div role="status" aria-live="polite" class="sr-only">
  Item added to cart successfully.
</div>
```

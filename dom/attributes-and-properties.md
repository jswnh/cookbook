# DOM Attributes, Properties & Dataset

A comprehensive guide to working with HTML attributes, DOM object properties, custom data attributes (`dataset`), and ARIA accessibility properties.

---

## 1. Attributes vs. Properties

A fundamental concept in the DOM is the distinction between **HTML Attributes** and **DOM Properties**.

| Concept | What It Is | Represents | Access Via |
| :--- | :--- | :--- | :--- |
| **HTML Attribute** | What is written in the initial HTML markup | Initial / default state | `getAttribute()`, `setAttribute()` |
| **DOM Property** | A property on the live JavaScript element object | Current live dynamic state | `element.propertyName` |

### Key Differences & Non-Synchronizing Values

For some attributes (like `id` or `title`), changing the property automatically updates the attribute and vice versa. However, for interactive form controls and URLs, they behave differently:

```html
<input id="username-input" type="text" value="initialValue">
<a id="home-link" href="/dashboard">Dashboard</a>
```

```javascript
const input = document.getElementById("username-input");

// 1. Form Inputs: value property vs value attribute
input.value = "newValueTypedByUser";

console.log(input.value); // "newValueTypedByUser" (Current live DOM property)
console.log(input.getAttribute("value")); // "initialValue" (Original HTML attribute)

// Resetting form returns input back to the attribute value!
input.value = input.getAttribute("value"); // resets back to "initialValue"

// 2. Relative URLs vs Absolute URLs
const link = document.getElementById("home-link");
console.log(link.getAttribute("href")); // "/dashboard" (Raw string in HTML)
console.log(link.href); // "https://example.com/dashboard" (Full resolved URL)
```

---

## 2. Standard Attribute Methods

| Method | Description |
| :--- | :--- |
| `element.getAttribute(name)` | Returns the string value of the attribute, or `null` if not present. |
| `element.setAttribute(name, value)` | Sets the attribute to the string representation of `value`. |
| `element.hasAttribute(name)` | Returns `true` if the attribute exists, `false` otherwise. |
| `element.removeAttribute(name)` | Removes the attribute completely from the element. |
| `element.toggleAttribute(name, force)` | Toggles a boolean attribute (adds if absent, removes if present). |
| `element.getAttributeNames()` | Returns an array of all attribute names present on the element. |

### `toggleAttribute()` (Modern Standard)
Simplifies adding and removing boolean attributes without manual `if` checks:

```javascript
const submitBtn = document.querySelector("#submit-btn");

// Toggle disabled state:
submitBtn.toggleAttribute("disabled");

// Explicit boolean flag (force: true adds it, false removes it)
const isSubmitting = true;
submitBtn.toggleAttribute("disabled", isSubmitting);
submitBtn.toggleAttribute("aria-busy", isSubmitting);
```

---

## 3. Handling Boolean Attributes

Boolean attributes (such as `disabled`, `checked`, `required`, `readonly`, `hidden`, `open`) are considered `true` if they are present in the markup, regardless of their string value (even `disabled="false"` means the element **is disabled**).

```html
<!-- All of these represent a DISABLED button! -->
<button disabled>Button 1</button>
<button disabled="">Button 2</button>
<button disabled="disabled">Button 3</button>
<button disabled="false">Button 4 (STILL DISABLED in HTML!)</button>
```

```javascript
const button = document.querySelector("button");

// Proper way to enable/disable using DOM properties (Boolean):
button.disabled = true;  // Disables the button
button.disabled = false; // Enables the button

// If using attributes, you MUST remove the attribute entirely to make it false:
button.removeAttribute("disabled");
```

---

## 4. Custom Data Attributes (`dataset`)

The `data-*` attributes allow storing custom data on elements. In JavaScript, they are accessed via the `element.dataset` object with automatic **kebab-case to camelCase** conversion.

### Name Mapping Rules

| HTML Attribute | JavaScript Property |
| :--- | :--- |
| `data-user-id` | `element.dataset.userId` |
| `data-role` | `element.dataset.role` |
| `data-action-url` | `element.dataset.actionUrl` |
| `data-max-retry-count` | `element.dataset.maxRetryCount` |

### Reading, Writing & Deleting Data Attributes

```html
<div
  id="user-card"
  data-user-id="481516"
  data-is-admin="true"
  data-permissions="read,write,delete"
>
  Alex Mercer
</div>
```

```javascript
const card = document.getElementById("user-card");

// 1. Reading values (always returned as strings!)
const userId = Number(card.dataset.userId); // Convert string to number: 481516
const isAdmin = card.dataset.isAdmin === "true"; // Convert string to boolean: true
const permissions = card.dataset.permissions.split(","); // ['read', 'write', 'delete']

// 2. Writing values (automatically converted to string in HTML)
card.dataset.lastLogin = new Date().toISOString(); // Sets data-last-login="..."
card.dataset.status = "active"; // Sets data-status="active"

// 3. Deleting values (removes the attribute from HTML)
delete card.dataset.permissions;

// 4. Querying elements via dataset attributes in CSS / DOM
const activeAdmins = document.querySelectorAll(
  "[data-is-admin='true'][data-status='active']"
);
```

---

## 5. Accessibility (ARIA) Attributes & IDL Properties

Modern browsers provide **ARIA IDL (Interface Definition Language) reflection properties**, allowing you to set ARIA attributes directly as camelCase properties on elements instead of calling `setAttribute('aria-*')`.

| HTML Attribute | Modern DOM Property | Description |
| :--- | :--- | :--- |
| `aria-label="..."` | `el.ariaLabel` | Accessible name for screen readers |
| `aria-expanded="..."`| `el.ariaExpanded` | Indicates if an accordion/menu is open |
| `aria-hidden="..."`  | `el.ariaHidden` | Hides decorative elements from screen readers |
| `aria-busy="..."`    | `el.ariaBusy` | Informs assistive tech that element is updating |
| `aria-disabled="..."`| `el.ariaDisabled` | Semantic disabled state without disabling focus |
| `role="..."`         | `el.role` | Defines element semantic role |

### Accessible Accordion Controller Example

```javascript
class AccessibleAccordion {
  constructor(triggerBtn, contentPanel) {
    this.button = triggerBtn;
    this.panel = contentPanel;

    // Initialize ARIA states
    this.button.role = "button";
    this.button.ariaExpanded = "false";
    this.panel.ariaHidden = "true";

    this.button.addEventListener("click", () => this.toggle());
  }

  toggle() {
    const isExpanded = this.button.ariaExpanded === "true";
    const nextState = !isExpanded;

    // Update ARIA properties directly
    this.button.ariaExpanded = String(nextState);
    this.panel.ariaHidden = String(!nextState);

    // Update visual state
    this.panel.classList.toggle("is-open", nextState);
  }
}

const toggleBtn = document.querySelector("#faq-trigger-1");
const content = document.querySelector("#faq-panel-1");
new AccessibleAccordion(toggleBtn, content);
```

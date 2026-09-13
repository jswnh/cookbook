# Shadow DOM & Web Components

A comprehensive guide to building native, encapsulated Web Components using Custom Elements, Shadow DOM, HTML templates, and slots.

---

## 1. The Web Components Architecture

Web Components consist of three core web standards:

1. **Custom Elements**: JavaScript APIs to define custom HTML tags with lifecycle callbacks.
2. **Shadow DOM**: Encapsulated DOM trees and scoped CSS styles isolated from the main document.
3. **HTML Templates & Slots**: Reusable markup fragments (`<template>`) and flexible content insertion points (`<slot>`).

---

## 2. Custom Elements Lifecycle Callbacks

To create a custom element, extend the native `HTMLElement` class and register it with `customElements.define()`.

> [!IMPORTANT]
> Custom element tag names **must** contain a hyphen (e.g. `user-avatar`, `nav-bar`) to distinguish them from standard HTML elements.

```javascript
class UserBadge extends HTMLElement {
  // 1. Declare which attributes to observe
  static get observedAttributes() {
    return ["status", "count"];
  }

  constructor() {
    super();
    // Element instantiated (good place to attach shadow DOM)
  }

  // Called when element is inserted into the document DOM
  connectedCallback() {
    this.render();
  }

  // Called when element is removed from the document DOM
  disconnectedCallback() {
    // Teardown event listeners, intervals, observers
  }

  // Called when an observed attribute is added, removed, or changed
  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue) {
      this.render();
    }
  }

  // Called when element is adopted into a new document (e.g., iframe)
  adoptedCallback() {}

  render() {
    const status = this.getAttribute("status") || "offline";
    this.textContent = `Status: ${status}`;
  }
}

// Register the custom tag with the browser
customElements.define("user-badge", UserBadge);
```

Usage in HTML:

```html
<user-badge status="online"></user-badge>
```

---

## 3. Shadow DOM & Style Encapsulation

Attaching a Shadow DOM isolates internal markup and styles from the outer page. CSS rules declared inside a shadow root cannot leak out, and global CSS rules cannot leak in.

```javascript
class ProfileCard extends HTMLElement {
  constructor() {
    super();

    // Attach open shadow root (accessible via this.shadowRoot)
    const shadow = this.attachShadow({ mode: "open" });

    shadow.innerHTML = `
      <style>
        /* Scoped styles only apply inside this component */
        :host {
          display: block;
          border: 1px solid var(--card-border, #e2e8f0);
          border-radius: 12px;
          padding: 1rem;
          background: var(--card-bg, #ffffff);
          font-family: system-ui, sans-serif;
        }

        :host([featured]) {
          box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
          border-color: #3b82f6;
        }

        .name {
          font-weight: 700;
          font-size: 1.1rem;
          margin: 0;
          color: #1e293b;
        }
      </style>

      <div class="card-inner">
        <h3 class="name">Anonymous</h3>
        <div class="bio">
          <!-- Content projected from the outside document -->
          <slot name="bio">No biography provided.</slot>
        </div>
      </div>
    `;
  }

  connectedCallback() {
    if (this.hasAttribute("name")) {
      this.shadowRoot.querySelector(".name").textContent = this.getAttribute("name");
    }
  }
}

customElements.define("profile-card", ProfileCard);
```

Usage with slotted markup:

```html
<profile-card name="Elena Rostova" featured>
  <p slot="bio">Frontend engineer specializing in Web Components & WebGL.</p>
</profile-card>
```

---

## 4. Shadow DOM CSS Selectors

| Selector | Matches | Purpose |
| :--- | :--- | :--- |
| **`:host`** | The custom element itself | Styles the component root container |
| **`:host([attribute])`** | The host when it has a specific attribute | e.g. `:host([disabled])` or `:host([active])` |
| **`:host-context(selector)`**| The host when an ancestor matches selector | e.g. `:host-context(.dark-theme)` |
| **`::slotted(selector)`** | Light DOM elements projected into a `<slot>` | Styles projected child content |

```css
/* Example inside Shadow DOM style tag */
:host {
  color: var(--text-color, #111827); /* CSS variables cross shadow boundaries! */
}

:host-context(.dark-mode) {
  background-color: #1f2937;
  color: #f9fafb;
}

::slotted(p) {
  margin: 0.5rem 0;
  line-height: 1.5;
}
```

---

## 5. Working with Slots (`<slot>`) in JavaScript

The `<slot>` element creates a placeholder inside the Shadow DOM that projects external children (Light DOM) into the component.

```javascript
const bioSlot = this.shadowRoot.querySelector("slot[name='bio']");

// Listen for slotted content changes
bioSlot.addEventListener("slotchange", () => {
  // Retrieve assigned nodes (including text)
  const nodes = bioSlot.assignedNodes();

  // Retrieve assigned elements only
  const elements = bioSlot.assignedElements();
  console.log("Slotted elements changed:", elements);
});
```

---

## 6. Declarative Shadow DOM (Baseline 2024 / SSR)

Declarative Shadow DOM allows server-rendered HTML to define a shadow root **without any client-side JavaScript**, eliminating layout shifts and enabling full SSR (Server-Side Rendering) for Web Components.

```html
<profile-card>
  <template shadowrootmode="open">
    <style>
      :host {
        display: block;
        padding: 1rem;
        background: #f8fafc;
        border-radius: 8px;
      }
    </style>
    <h2>Server-Rendered Component</h2>
    <slot></slot>
  </template>

  <p>This content is seamlessly projected into the shadow root upon initial paint!</p>
</profile-card>
```

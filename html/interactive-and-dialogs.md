## 1. The Native `<dialog>` Element

The HTML5 `<dialog>` element represents a native modal or non-modal popup window. It provides built-in keyboard accessibility (Escape to dismiss), automatic focus management, and rendering in the browser's top layer.

### Native Dialog Types

| Type | Launch Method | Traps Focus | Dismissible by Esc | Backdrop |
| :--- | :--- | :--- | :--- | :--- |
| **Modal Dialog** | `dialog.showModal()` | Yes | Yes | Yes (styles via `::backdrop`) |
| **Non-Modal Dialog** | `dialog.show()` | No | No | No |

### Complete `<dialog>` Example

```html
<!-- Trigger Button -->
<button type="button" id="open-btn">Open Profile Modal</button>

<!-- Native Dialog -->
<dialog id="profile-dialog" aria-labelledby="dialog-title">
  <!-- Form with method="dialog" closes the modal on submit without JavaScript -->
  <form method="dialog">
    <h2 id="dialog-title">User Settings</h2>
    <p>Configure your workspace preferences.</p>

    <label for="username">Display Name:</label>
    <input type="text" id="username" name="username" value="Alex">

    <div class="dialog-actions">
      <!-- Clicking Cancel returns "cancel" value to dialog.returnValue -->
      <button type="submit" value="cancel">Cancel</button>
      <!-- Clicking Confirm returns "confirm" value -->
      <button type="submit" value="confirm">Save Changes</button>
    </div>
  </form>
</dialog>

<script>
  const dialog = document.getElementById("profile-dialog");
  const openBtn = document.getElementById("open-btn");

  openBtn.addEventListener("click", () => {
    dialog.showModal(); // Opens as top-layer modal
  });

  dialog.addEventListener("close", () => {
    console.log("Dialog closed with return value:", dialog.returnValue);
  });
</script>
```

---

## 2. The Popover API

The HTML Popover API provides declarative, zero-JavaScript tooltips, menus, and popups rendered in the top layer.

### Popover Attributes

| Attribute | Elements | Values | Purpose |
| :--- | :--- | :--- | :--- |
| **`popover`** | Any element (popover target) | `auto` (default), `manual` | Turns an element into a popover. `auto` provides light dismiss (click outside / Esc closes). |
| **`popovertarget`** | `<button>`, `<input>` | Target element `id` | Connects a trigger button to a popover element. |
| **`popovertargetaction`** | `<button>`, `<input>` | `toggle` (default), `show`, `hide` | Specifies the action to take when triggering. |

### Declarative Zero-JS Popover Example

```html
<!-- Trigger Button -->
<button type="button" popovertarget="notifications-menu">
  Notifications (3)
</button>

<!-- Popover Container -->
<div id="notifications-menu" popover="auto">
  <h3>Recent Alerts</h3>
  <ul>
    <li>New pull request assigned</li>
    <li>Build succeeded for branch main</li>
    <li>Security review approved</li>
  </ul>
  <!-- Explicit close button inside popover -->
  <button type="button" popovertarget="notifications-menu" popovertargetaction="hide">
    Close
  </button>
</div>
```

---

## 3. Disclosures & Exclusive Accordions (`<details>`, `<summary>`)

The `<details>` element creates a native toggleable disclosure widget without requiring JavaScript.

### Exclusive Accordion (HTML Living Standard)

By adding the **`name`** attribute to multiple `<details>` elements, browsers automatically enforce that only one item can remain open at any given time (exclusive accordion).

```html
<section class="faq-accordion">
  <h2>Frequently Asked Questions</h2>

  <!-- Sharing the same name="faq" makes them mutually exclusive -->
  <details name="faq" open>
    <summary>What is the HTML Living Standard?</summary>
    <p>It is the continuously maintained specification for HTML managed by the WHATWG.</p>
  </details>

  <details name="faq">
    <summary>Do I need JavaScript for modal dialogs?</summary>
    <p>No, basic open/close behaviors can be controlled via forms with method="dialog".</p>
  </details>

  <details name="faq">
    <summary>Are CSS container queries supported across browsers?</summary>
    <p>Yes, container queries are part of the web platform Baseline across all major engines.</p>
  </details>
</section>
```

---

## 4. Templates & Slots (`<template>`, `<slot>`)

### The `<template>` Tag

The `<template>` tag holds client-side HTML markup that is not rendered on page load, but can be instantiated and cloned dynamically at runtime using JavaScript.

```html
<template id="user-card-template">
  <div class="user-card">
    <h3 class="user-name"></h3>
    <p class="user-email"></p>
    <button class="user-action">View Profile</button>
  </div>
</template>

<script>
  const template = document.getElementById("user-card-template");
  const clone = template.content.cloneNode(true);
  clone.querySelector(".user-name").textContent = "Sarah Connor";
  clone.querySelector(".user-email").textContent = "sarah@example.com";
  document.body.appendChild(clone);
</script>
```

### The `<slot>` Tag (Web Components)

Used inside Web Component Shadow DOMs as a placeholder that gets filled by child markup passed from outside the component.

```html
<template id="custom-alert-template">
  <div class="alert-box">
    <span class="icon">⚠️</span>
    <!-- The external text gets projected here -->
    <slot name="message">Default warning notice</slot>
  </div>
</template>
```

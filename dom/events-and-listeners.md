# DOM Events & Listeners

A comprehensive guide to the DOM event lifecycle, propagation phases, high-performance event delegation, modern listener options, and native cleanup with `AbortSignal`.

---

## 1. The DOM Event Flow

When an event occurs on an element (e.g., clicking a button inside a card), the browser dispatches the event through three distinct phases:

```
        Window
          |   ^
1. Capture|   | 3. Bubbling
   Phase  |   |    Phase
          v   |
        Card Container
          |   ^
          v   |
      Button (2. Target Phase)
```

1. **Capturing Phase (Trickling)**: The event descends from `Window` down the DOM tree toward the target element.
2. **Target Phase**: The event reaches the originating target node (`e.target`).
3. **Bubbling Phase**: The event ascends back up from the target element through all its ancestor elements to `Window`.

> [!NOTE]
> By default, `addEventListener()` only listens during the **Bubbling Phase**. To listen during the Capturing Phase, pass `{ capture: true }`.

---

## 2. `addEventListener` Options & Configuration

Modern browsers support an options object as the third argument to `addEventListener(type, listener, options)`:

| Option | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `capture` | `Boolean` | `false` | If `true`, fires during the Capturing phase. |
| `once` | `Boolean` | `false` | If `true`, automatically removes the listener after firing once. |
| `passive` | `Boolean` | `false` | If `true`, guarantees `preventDefault()` will never be called. Drastically improves scrolling performance on `touchstart` and `wheel`. |
| `signal` | `AbortSignal` | - | Removes the listener when the associated `AbortSignal` is aborted. |

### Example: Listener Options

```javascript
// 1. One-time execution: automatically self-removes
const downloadBtn = document.querySelector("#download-btn");
downloadBtn.addEventListener("click", () => {
  console.log("Download initiated once");
}, { once: true });

// 2. Passive scroll listener for 60fps performance
window.addEventListener("scroll", () => {
  // Browser can scroll immediately without waiting for JS execution
  const scrollY = window.scrollY;
}, { passive: true });
```

---

## 3. Native Listener Cleanup with `AbortController`

Instead of manually maintaining function references and calling `removeEventListener()`, modern JavaScript uses `AbortController` to cleanly remove one or multiple listeners at once.

```javascript
// Managing multiple listeners with a single controller
const controller = new AbortController();
const { signal } = controller;

const modal = document.querySelector("#user-modal");
const saveBtn = document.querySelector("#save-btn");
const cancelBtn = document.querySelector("#cancel-btn");

// Bind listeners with signal
saveBtn.addEventListener("click", handleSave, { signal });
cancelBtn.addEventListener("click", handleCancel, { signal });
window.addEventListener("keydown", handleEscapeKey, { signal });

function closeModal() {
  modal.classList.add("hidden");

  // Single call removes ALL attached event listeners simultaneously!
  controller.abort();
  console.log("All modal event listeners cleanly disconnected.");
}
```

### Auto-Expiring Listeners with `AbortSignal.timeout()`

```javascript
const banner = document.querySelector(".promo-banner");

// Listener automatically detaches after 10 seconds
banner.addEventListener("click", () => {
  console.log("Promo clicked within 10s");
}, { signal: AbortSignal.timeout(10000) });
```

---

## 4. The Event Object: Target vs CurrentTarget

| Property / Method | Description |
| :--- | :--- |
| `event.target` | The **actual innermost element** that triggered the event (e.g. child `<span>` or `<svg>`). |
| `event.currentTarget` | The element to which the **event listener is attached**. Equivalent to `this` in regular functions. |
| `event.preventDefault()` | Prevents default browser action (e.g. form submission, link navigation). |
| `event.stopPropagation()` | Stops the event from bubbling further up or capturing down the DOM tree. |
| `event.stopImmediatePropagation()` | Stops bubbling AND prevents other listeners on the **same element** from executing. |
| `event.defaultPrevented` | Returns `true` if `preventDefault()` was called. |

---

## 5. The Event Delegation Pattern

Instead of attaching hundreds of event listeners to individual child elements, attach a single listener to a common ancestor container. Use `event.target.closest()` to identify the clicked child.

### Why Event Delegation?
- **Extreme Memory Efficiency**: 1 listener instead of 1,000.
- **Dynamic Elements**: Automatically works for newly added DOM elements without rebinding.

```html
<ul id="task-list">
  <li data-id="1">
    <span>Write tests</span>
    <button type="button" class="btn-delete" aria-label="Delete">🗑️</button>
  </li>
  <li data-id="2">
    <span>Ship release</span>
    <button type="button" class="btn-delete" aria-label="Delete">🗑️</button>
  </li>
</ul>
```

```javascript
const taskList = document.querySelector("#task-list");

taskList.addEventListener("click", (event) => {
  // 1. Check if the click occurred on or inside a delete button
  const deleteBtn = event.target.closest(".btn-delete");
  if (!deleteBtn) return; // Ignore clicks elsewhere in <ul>

  // 2. Locate the parent task item
  const taskItem = deleteBtn.closest("li");
  const taskId = taskItem.dataset.id;

  console.log(`Deleting task ${taskId}`);
  taskItem.remove();
});
```

---

## 6. Custom Events (`CustomEvent`)

Custom events decouple components by allowing elements to emit domain-specific events with attached payloads.

```javascript
const cartElement = document.querySelector("#shopping-cart");

// 1. Listen for custom event
cartElement.addEventListener("cart:updated", (event) => {
  const { itemCount, totalPrice } = event.detail;
  console.log(`Cart has ${itemCount} items totaling $${totalPrice}`);
});

// 2. Dispatch custom event with detail payload
function notifyCartChange(count, total) {
  const customEvent = new CustomEvent("cart:updated", {
    detail: {
      itemCount: count,
      totalPrice: total
    },
    bubbles: true,    // Allow event to bubble up the DOM
    cancelable: true  // Allow listeners to call preventDefault()
  });

  cartElement.dispatchEvent(customEvent);
}

notifyCartChange(3, 89.99);
```

---

## 7. Essential Modern Event Types

### Pointer Events (Unifies Mouse, Pen & Touch)
Pointer events replace disparate mouse and touch handlers with a single unified API.

```javascript
const draggable = document.querySelector(".slider-thumb");

draggable.addEventListener("pointerdown", (e) => {
  // Locks all subsequent pointer events to this element even if cursor leaves
  draggable.setPointerCapture(e.pointerId);

  const onPointerMove = (moveEvent) => {
    draggable.style.transform = `translateX(${moveEvent.clientX}px)`;
  };

  const onPointerUp = () => {
    draggable.releasePointerCapture(e.pointerId);
    window.removeEventListener("pointermove", onPointerMove);
    window.removeEventListener("pointerup", onPointerUp);
  };

  window.addEventListener("pointermove", onPointerMove);
  window.addEventListener("pointerup", onPointerUp);
});
```

### Keyboard Events (`keydown`, `e.key`, `e.code`)

```javascript
window.addEventListener("keydown", (event) => {
  // Prefer event.key for logical characters, event.code for physical key locations
  if (event.key === "Escape") {
    closeActiveModal();
  }

  // Keyboard shortcuts (Cmd/Ctrl + K)
  if ((event.metaKey || event.ctrlKey) && event.key.toLowerCase() === "k") {
    event.preventDefault(); // Prevent browser search bar focus
    openQuickSearch();
  }
});
```

### Form Events & Modern `FormData`

```javascript
const form = document.querySelector("#registration-form");

form.addEventListener("submit", (event) => {
  event.preventDefault(); // Stop full page reload

  // Native FormData extraction
  const formData = new FormData(form);

  // Convert form data directly to a plain JavaScript object
  const data = Object.fromEntries(formData.entries());
  console.log("Submitting payload:", data);
});
```

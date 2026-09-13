# DOM Observers & Page Lifecycle

A comprehensive guide to modern asynchronous observers (`IntersectionObserver`, `ResizeObserver`, `MutationObserver`) and document lifecycle management.

---

## 1. `IntersectionObserver`

The `IntersectionObserver` API asynchronously monitors when a target element enters or exits an ancestor element or the top-level viewport. It completely eliminates laggy scroll event handlers.

### Key Options

| Option | Type | Default | Purpose |
| :--- | :--- | :--- | :--- |
| `root` | `Element \| Document \| null` | `null` (Viewport) | The element used as the viewport for checking visibility. |
| `rootMargin` | `String` | `"0px"` | Margin around root (e.g. `"200px 0px"` to pre-load content before it enters screen). |
| `threshold` | `Number \| Number[]` | `0` | Percentage of target visibility needed to trigger (e.g., `0.5` = 50% visible). |

---

### Use Case 1: High-Performance Image Lazy Loading

```javascript
const lazyImages = document.querySelectorAll("img[data-src]");

const imageObserver = new IntersectionObserver((entries, observer) => {
  entries.forEach((entry) => {
    if (!entry.isIntersecting) return;

    const img = entry.target;
    // Swap data-src into real src
    img.src = img.dataset.src;
    img.removeAttribute("data-src");
    img.classList.add("loaded");

    // Stop observing once loaded
    observer.unobserve(img);
  });
}, {
  rootMargin: "150px 0px" // Preload 150px before entering viewport
});

lazyImages.forEach((img) => imageObserver.observe(img));
```

---

### Use Case 2: Scrollspy (Active Section Highlighting)

```javascript
const navLinks = document.querySelectorAll("nav a");
const sections = document.querySelectorAll("section[id]");

const sectionObserver = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      const activeId = entry.target.id;
      navLinks.forEach((link) => {
        link.classList.toggle("active", link.getAttribute("href") === `#${activeId}`);
      });
    }
  });
}, {
  threshold: 0.6 // Trigger when 60% of section is visible
});

sections.forEach((section) => sectionObserver.observe(section));
```

---

## 2. `ResizeObserver`

`ResizeObserver` reports element-level dimension changes. Unlike `window.onresize`, it fires whenever a specific element resizes (e.g., sidebar collapse, layout shifts, container query effects).

### Example: Responsive Canvas / Widget

```javascript
const chartContainer = document.querySelector("#chart-container");
const canvas = chartContainer.querySelector("canvas");

const resizeObserver = new ResizeObserver((entries) => {
  for (const entry of entries) {
    // entry.contentBoxSize provides exact pixel dimensions
    let width, height;
    if (entry.contentBoxSize) {
      const box = Array.isArray(entry.contentBoxSize)
        ? entry.contentBoxSize[0]
        : entry.contentBoxSize;
      width = box.inlineSize;
      height = box.blockSize;
    } else {
      // Fallback
      width = entry.contentRect.width;
      height = entry.contentRect.height;
    }

    // Adapt canvas rendering surface to container size
    canvas.width = width;
    canvas.height = height;
    redrawChart(width, height);
  }
});

resizeObserver.observe(chartContainer);

// When cleaning up:
// resizeObserver.disconnect();
```

---

## 3. `MutationObserver`

`MutationObserver` monitors DOM tree changes (such as added/removed children, modified attributes, or text content updates).

### Configuration Options (`MutationObserverInit`)

| Option | Type | Description |
| :--- | :--- | :--- |
| `childList` | `Boolean` | Watch additions and removals of child nodes. |
| `subtree` | `Boolean` | Watch target and **all descendants** (recursive). |
| `attributes` | `Boolean` | Watch attribute modifications. |
| `attributeFilter` | `String[]` | Specific attribute names to watch (e.g. `['class', 'data-status']`). |
| `attributeOldValue` | `Boolean` | Record prior attribute value. |
| `characterData` | `Boolean` | Watch text node updates. |

### Example: Watching for Theme or Status Attribute Changes

```javascript
const appRoot = document.documentElement;

const mutationObserver = new MutationObserver((mutationsList) => {
  for (const mutation of mutationsList) {
    if (mutation.type === "attributes" && mutation.attributeName === "data-theme") {
      const currentTheme = appRoot.getAttribute("data-theme");
      const oldTheme = mutation.oldValue;
      console.log(`Theme shifted from ${oldTheme} to ${currentTheme}`);
    }
  }
});

mutationObserver.observe(appRoot, {
  attributes: true,
  attributeFilter: ["data-theme"],
  attributeOldValue: true
});

// Programmatic change triggers observer
appRoot.setAttribute("data-theme", "dark");
```

---

## 4. Document Readiness & Page Lifecycle

### `DOMContentLoaded` vs `load` vs `readyState`

```javascript
// 1. Check current document state
if (document.readyState === "loading") {
  document.addEventListener("DOMContentLoaded", initApp);
} else {
  // DOM is already parsed!
  initApp();
}

function initApp() {
  console.log("DOM tree fully parsed and interactive (styles/images may still load)");
}

// 2. Full Page Load (all images, stylesheets, iframes complete)
window.addEventListener("load", () => {
  console.log("Entire page and external assets loaded.");
});
```

---

## 5. Page Visibility API (`visibilitychange`)

Detects when the user minimizes the browser, switches tabs, or locks their device. Essential for pausing animations, suspending audio/video, and reducing battery drain.

```javascript
document.addEventListener("visibilitychange", () => {
  if (document.hidden) {
    console.log("Tab hidden: Pausing telemetry & animations");
    pauseGameLoop();
    muteAudio();
  } else {
    console.log("Tab visible: Resuming application");
    resumeGameLoop();
    unmuteAudio();
  }
});
```

---

## 6. Page Unload & BFCache (Back/Forward Cache)

Modern browsers use the **BFCache** (Back/Forward Cache) to restore entire pages instantaneously when users navigate with the Back/Forward buttons.

> [!WARNING]
> Never use the legacy `unload` event! Listening to `unload` completely disables BFCache eligibility across all browsers.

### Safe Navigation Lifecycle: `pageshow` and `pagehide`

```javascript
// 1. pageshow: runs on initial load AND when restored from BFCache
window.addEventListener("pageshow", (event) => {
  if (event.persisted) {
    console.log("Page was restored directly from BFCache (super fast!)");
  } else {
    console.log("Page loaded normally from network");
  }
});

// 2. pagehide: clean alternative to unload
window.addEventListener("pagehide", (event) => {
  // Send analytics/telemetry reliably on exit using keepalive fetch or sendBeacon
  const analyticsPayload = JSON.stringify({ sessionDuration: 420 });
  navigator.sendBeacon("/api/telemetry", analyticsPayload);
});
```

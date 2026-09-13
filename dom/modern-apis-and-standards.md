# Modern DOM APIs & Standards

A guide to modern, cutting-edge DOM APIs including the View Transitions API, Popover & Dialog scripting, Web Animations API (WAAPI), Fullscreen API, and Clipboard API.

---

## 1. View Transitions API

The View Transitions API provides a mechanism for animating DOM state mutations (e.g., sorting a list, switching tabs, updating a shopping cart) without complex CSS animation hacks.

### Single-Page Transition (`document.startViewTransition`)

```javascript
function updateUI(newContent) {
  // Check browser support for progressive enhancement
  if (!document.startViewTransition) {
    applyDOMChanges(newContent);
    return;
  }

  // Wraps the DOM update in an automatic smooth cross-fade transition
  const transition = document.startViewTransition(() => {
    applyDOMChanges(newContent);
  });

  // Await transition stages if needed
  transition.ready.then(() => {
    console.log("Pseudo-elements generated; animation active.");
  });

  transition.finished.then(() => {
    console.log("View transition completed.");
  });
}

function applyDOMChanges(newContent) {
  const container = document.querySelector("#main-content");
  container.textContent = newContent;
}
```

### Pairing with CSS `view-transition-name`
Give elements unique transition names so the browser animates them individually across layouts:

```css
/* Elements with the same transition name morph smoothly from old to new position */
.active-hero-card {
  view-transition-name: hero-card;
}
```

---

## 2. Dialog & Popover Scripting

### Native `<dialog>` Element Scripting

```javascript
const dialog = document.querySelector("#confirm-dialog");
const openBtn = document.querySelector("#open-dialog-btn");

// 1. Open as a top-layer modal (traps focus, backdrop rendered)
openBtn.addEventListener("click", () => {
  dialog.showModal();
});

// 2. Programmatically close with a return value
function handleConfirm() {
  dialog.close("confirmed"); // Sets dialog.returnValue = "confirmed"
}

// 3. Listen for the close event
dialog.addEventListener("close", () => {
  console.log("Dialog closed with status:", dialog.returnValue);
});

// 4. Listen for Esc dismissal
dialog.addEventListener("cancel", (event) => {
  console.log("User cancelled dialog with Escape key");
});
```

---

### Native Popover API Scripting

HTML popovers (`popover="auto"` or `popover="manual"`) live in the browser's top layer and can be controlled purely in JavaScript:

```javascript
const popoverEl = document.querySelector("#user-menu-popover");

// Programmatic control methods
popoverEl.showPopover();   // Opens popover
popoverEl.hidePopover();   // Closes popover
popoverEl.togglePopover(); // Toggles visibility

// Check if popover is currently open
const isOpen = popoverEl.matches(":popover-open");

// Listen for toggle events
popoverEl.addEventListener("toggle", (event) => {
  if (event.newState === "open") {
    console.log("Popover is now displayed");
  } else {
    console.log("Popover was closed");
  }
});
```

---

## 3. Web Animations API (WAAPI)

The Web Animations API allows developers to construct performant hardware-accelerated animations directly in JavaScript.

### `element.animate(keyframes, options)`

```javascript
const toast = document.querySelector(".toast-notification");

// Define keyframes and timing options
const animation = toast.animate(
  [
    { opacity: 0, transform: "translateY(30px) scale(0.95)" },
    { opacity: 1, transform: "translateY(0) scale(1)" }
  ],
  {
    duration: 350,
    easing: "cubic-bezier(0.16, 1, 0.3, 1)",
    fill: "forwards" // Retain final frame styles
  }
);

// Control playback programmatically
// animation.pause();
// animation.reverse();
// animation.playbackRate = 2.0; // Double speed

// Await completion using the native finished Promise
async function displayNotification() {
  await animation.finished;
  console.log("Toast animation completed successfully!");
}
displayNotification();
```

---

## 4. Native Clipboard API

The Async Clipboard API provides secure reading and writing of text and images from the system clipboard.

```javascript
// 1. Copy text to clipboard
async function copyToClipboard(text) {
  try {
    await navigator.clipboard.writeText(text);
    console.log("Copied to clipboard successfully!");
  } catch (err) {
    console.error("Failed to copy text:", err);
  }
}

// 2. Read text from clipboard
async function pasteFromClipboard() {
  try {
    const text = await navigator.clipboard.readText();
    console.log("Pasted text:", text);
    return text;
  } catch (err) {
    console.error("Clipboard read permission denied:", err);
  }
}
```

---

## 5. Fullscreen API

Allows presenting an element (video player, canvas game, image viewer) in full-screen mode.

```javascript
const videoPlayer = document.querySelector("#video-container");
const fullscreenToggle = document.querySelector("#fullscreen-btn");

fullscreenToggle.addEventListener("click", async () => {
  if (!document.fullscreenElement) {
    // Enter fullscreen mode
    await videoPlayer.requestFullscreen();
  } else {
    // Exit fullscreen mode
    await document.exitFullscreen();
  }
});

// React to fullscreen change
document.addEventListener("fullscreenchange", () => {
  const isFullscreen = document.fullscreenElement !== null;
  fullscreenToggle.textContent = isFullscreen ? "Exit Fullscreen" : "Enter Fullscreen";
});
```

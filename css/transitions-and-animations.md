## 1. CSS Transitions

CSS Transitions smoothly animate property value changes triggered by state changes (such as `:hover`, `:focus`, or class toggles).

### Transition Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`transition-property`** | Property name(s), `all`, `none` | Names of CSS properties to transition. |
| **`transition-duration`** | Time (`s`, `ms`) | Length of time the transition takes to complete. |
| **`transition-timing-function`** | `ease`, `linear`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier()` | Acceleration curve of the transition. |
| **`transition-delay`** | Time (`s`, `ms`) | Wait time before beginning transition. |
| **`transition-behavior`**| `normal`, `allow-discrete` | **Modern Baseline**: Allows animating discrete properties (like `display` and `overlay`). |
| **`transition`** | Shorthand | `<property> <duration> <timing-function> <delay>` |

### Animating `display: none` with `@starting-style`

Historically, `display` could not be transitioned. With `@starting-style` and `transition-behavior: allow-discrete`, elements can smoothly fade in from `display: none` or top-layer dialogs:

```css
.dropdown-menu {
  display: none;
  opacity: 0;
  transform: translateY(-8px);
  transition: opacity 0.3s ease, transform 0.3s ease, display 0.3s allow-discrete;
}

.dropdown-menu.is-open {
  display: block;
  opacity: 1;
  transform: translateY(0);

  /* Starting styles before first frame render */
  @starting-style {
    opacity: 0;
    transform: translateY(-8px);
  }
}
```

---

## 2. Transforms & Individual Transform Properties

Transforms alter coordinate space without triggering browser layout reflow.

### Modern Individual Transform Properties

Modern CSS allows declaring `translate`, `rotate`, and `scale` independently, avoiding the need to restate the entire `transform` string.

| Property | Values | Description |
| :--- | :--- | :--- |
| **`translate`** | `x [y [z]]` (e.g., `0 -4px`) | Repositions the element along coordinate axes. |
| **`rotate`** | Angle (e.g., `45deg`, `0.5turn`) | Rotates the element. |
| **`scale`** | Number (e.g., `1.05`, `1 0.8`) | Resizes element scale along X and Y. |
| **`transform`** | Transform function list | Traditional transform string (`translate()`, `rotate()`, `skew()`, `scale()`). |
| **`transform-origin`** | Coordinates (e.g., `50% 50%`, `top left`) | The pivot point around which transforms occur. |

```css
.interactive-card {
  transition: translate 0.2s ease, scale 0.2s ease;
}

.interactive-card:hover {
  translate: 0 -6px; /* Moves up 6px without needing transform: translateY(-6px) */
  scale: 1.02;       /* Scales up slightly */
}
```

---

## 3. Keyframe Animations (`@keyframes`)

Keyframe animations control intermediate steps in a CSS animation sequence.

### Animation Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`animation-name`** | Ident matches `@keyframes` name | Specifies which `@keyframes` rule to play. |
| **`animation-duration`**| Time (`s`, `ms`) | Time required for one cycle of the animation. |
| **`animation-timing-function`**| `ease`, `linear`, `cubic-bezier()` | Acceleration curve across keyframes. |
| **`animation-delay`** | Time (`s`, `ms`) | Delay before animation starts. |
| **`animation-iteration-count`**| Integer, `infinite` | Number of times animation plays. |
| **`animation-direction`**| `normal`, `reverse`, `alternate`, `alternate-reverse` | Plays forward, backward, or alternates direction. |
| **`animation-fill-mode`**| `none`, `forwards`, `backwards`, `both` | Style application before and after animation execution. |
| **`animation-play-state`**| `running`, `paused` | Can pause/resume animations (e.g., on hover). |
| **`animation`** | Shorthand | Combines all animation properties into one declaration. |

```css
@keyframes pulse-ring {
  0% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(59, 130, 246, 0.7);
  }
  70% {
    transform: scale(1);
    box-shadow: 0 0 0 10px rgba(59, 130, 246, 0);
  }
  100% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(59, 130, 246, 0);
  }
}

.status-badge-active {
  animation: pulse-ring 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}
```

---

## 4. Scroll-Driven Animations (CSS Animation Level 2)

Scroll-driven animations link keyframe progress directly to the user's scroll position instead of elapsed time, entirely without JavaScript.

### Reading Scroll Progress with `animation-timeline`

| Function | Description |
| :--- | :--- |
| **`scroll()`** | Ties animation to scroll position of the nearest scrollable ancestor. |
| **`view()`** | Ties animation to element's visibility as it enters and leaves the scrollport. |
| **`animation-range`** | Specifies where in the scroll progress animation begins and ends (`entry`, `exit`, `cover`, `contain`). |

### Scroll Progress Indicator (Zero JavaScript)

```css
/* Progress bar pinned at top of page */
.scroll-progress-bar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 4px;
  background-color: #2563eb;
  transform-origin: 0% 50%;
  
  /* Link animation timeline to page scroll */
  animation: grow-progress linear;
  animation-timeline: scroll();
}

@keyframes grow-progress {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}
```

### Reveal-on-Scroll Card Animation

```css
.fade-in-card {
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 10% cover 30%;
}

@keyframes reveal {
  from {
    opacity: 0;
    transform: translateY(40px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
```

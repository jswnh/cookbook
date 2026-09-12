## 1. Modern Color Spaces & Formats

CSS Color Module Level 4 and 5 introduce perceptually uniform and wide-gamut color spaces, making color palettes more predictable and accessible.

### Color Formats Reference

| Format | Syntax | Example | Description |
| :--- | :--- | :--- | :--- |
| **Hexadecimal** | `#RRGGBB[AA]` | `#2563eb`, `#2563eb80` | Hex RGB channels with optional alpha channel. |
| **Modern RGB** | `rgb(R G B [/ A])` | `rgb(37 99 235 / 80%)` | Space-separated syntax with forward-slash opacity. |
| **Modern HSL** | `hsl(H S% L% [/ A])`| `hsl(221 83% 53% / 0.8)` | Hue, Saturation, Lightness. |
| **OKLCH** | `oklch(L C H [/ A])`| `oklch(0.6 0.22 250 / 1)` | **Modern Gold Standard**: Perceptually uniform lightness, chroma, and hue. |
| **Display P3** | `color(display-p3 R G B)`| `color(display-p3 0.1 0.9 0.3)` | Wide-gamut color for modern monitors and mobile screens. |
| **Keywords** | `currentcolor`, `transparent` | `currentcolor` | Inherits the element's current text `color` value. |

### Why OKLCH is Preferred for Modern Design Systems

Traditional HSL produces vastly different perceived brightness depending on hue (e.g., pure blue at 50% lightness looks dark, whereas pure yellow at 50% lightness looks neon bright). OKLCH guarantees consistent perceived brightness across all hues.

```css
:root {
  /* OKLCH: Lightness (0-1), Chroma (saturation/purity), Hue (0-360) */
  --primary: oklch(0.6 0.2 250);
  --success: oklch(0.6 0.2 145);
  --warning: oklch(0.6 0.2 80);
  --danger:  oklch(0.6 0.2 25);
}
```

### Color Mixing (`color-mix()`)

Dynamically creates tints, shades, or blended colors directly in CSS without preprocessors:

```css
:root {
  --brand: oklch(0.65 0.24 260);
  
  /* Create 20% light tint */
  --brand-light: color-mix(in oklch, var(--brand) 20%, white);
  
  /* Create 30% transparent version */
  --brand-alpha: color-mix(in oklch, var(--brand) 70%, transparent);
}
```

---

## 2. Background Properties Reference

| Property | Values | Description |
| :--- | :--- | :--- |
| **`background-color`** | Any valid color format | Background fill color. |
| **`background-image`** | `url(...)`, `linear-gradient()`, `radial-gradient()`, `conic-gradient()` | Background graphic or generated gradient. |
| **`background-repeat`**| `repeat`, `no-repeat`, `repeat-x`, `repeat-y`, `space`, `round` | Tiling behavior for background images. |
| **`background-position`**| `center`, `top left`, `right 20px bottom 10px` | Alignment and offset of image within container. |
| **`background-size`** | `cover`, `contain`, `auto`, `100% auto` | Sizing strategy (`cover` fills, `contain` fits). |
| **`background-attachment`**| `scroll`, `fixed`, `local` | Whether background scrolls with viewport or content. |
| **`background-clip`** | `border-box`, `padding-box`, `content-box`, `text` | Painting area boundary (`text` for gradient text). |
| **`background-origin`**| `border-box`, `padding-box`, `content-box` | Positioning coordinate origin for the background image. |
| **`background`** | Shorthand | Combines all background properties into a single declaration. |

### Gradient Text Pattern

```css
.gradient-heading {
  background-image: linear-gradient(135deg, #6366f1, #ec4899);
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
}
```

---

## 3. Shadows & Visual Filters

### Box Shadow & Text Shadow

$$\text{box-shadow: [inset] } X \quad Y \quad \text{blur} \quad \text{spread} \quad \text{color}$$

```css
/* Smooth layered card elevation */
.card-elevation {
  box-shadow: 
    0 1px 3px rgba(0, 0, 0, 0.05),
    0 8px 24px rgba(0, 0, 0, 0.08);
}

/* Text glow */
.glowing-text {
  text-shadow: 0 0 12px oklch(0.7 0.25 150);
}
```

### Backdrop Filter (Frosted Glass UI)

The `backdrop-filter` property applies graphical effects (like blurring or color shifting) to the area **behind** an element.

```css
.glassmorphism-card {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(16px) saturate(180%);
  -webkit-backdrop-filter: blur(16px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
}
```

### Graphical Filters (`filter`)

- **`filter: blur(8px)`**: Gaussian blur.
- **`filter: brightness(1.2)`**: Brightens above 1.0, darkens below 1.0.
- **`filter: contrast(150%)`**: Increases or decreases contrast.
- **`filter: drop-shadow(0 4px 6px rgba(0,0,0,0.2))`**: Drops shadow conforming to PNG/SVG alpha outlines (unlike `box-shadow` which creates a box).
- **`filter: grayscale(100%)`**: Converts to black and white.
- **`filter: hue-rotate(90deg)`**: Shifts color hues.

---

## 4. Clip Path & Blend Modes

```css
/* Circular avatar crop */
.avatar {
  clip-path: circle(50% at 50% 50%);
}

/* Diagonal section divider */
.angled-banner {
  clip-path: polygon(0 0, 100% 0, 100% 85%, 0 100%);
}

/* Blending background image with a tint overlay */
.hero-blend {
  background-image: url('pattern.png'), linear-gradient(to right, #0f172a, #1e293b);
  background-blend-mode: overlay;
}
```

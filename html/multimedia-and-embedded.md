## 1. Images & Responsive Media

### The Modern `<img>` Tag

Modern `<img>` elements include performance, security, and responsive attributes to optimize Core Web Vitals (especially Cumulative Layout Shift - CLS, and Largest Contentful Paint - LCP).

| Attribute | Accepted Values | Purpose |
| :--- | :--- | :--- |
| **`src`** | URL / file path | Resource URL of the image. |
| **`alt`** | Descriptive string | Accessible text alternative for screen readers and when images fail to load. Use `alt=""` for purely decorative images. |
| **`width` / `height`** | Positive integers (pixels) | Provides the intrinsic aspect ratio so the browser reserves layout space before download (prevents CLS). |
| **`loading`** | `lazy`, `eager` | Defers loading off-screen images until scrolled near the viewport (`lazy`). |
| **`decoding`** | `async`, `sync`, `auto` | Decodes image off the main thread to avoid dropping frames (`async`). |
| **`fetchpriority`**| `high`, `low`, `auto` | Prioritizes downloading critical LCP images (`high`). |
| **`srcset`** | Comma-separated URL and width/density descriptors | Provides a list of image candidates for different screen resolutions. |
| **`sizes`** | Media queries with slot widths | Informs the browser what slot size the image will occupy at different viewport widths. |

#### High-Performance Responsive Image Example

```html
<img 
  src="/images/hero-800.jpg" 
  srcset="/images/hero-400.jpg 400w, /images/hero-800.jpg 800w, /images/hero-1600.jpg 1600w"
  sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 800px"
  alt="Modern open-plan software development office"
  width="800" 
  height="450" 
  loading="lazy" 
  decoding="async"
>
```

---

## 2. The `<picture>` Element

The `<picture>` wrapper allows multi-format negotiation (e.g. Next-Gen AVIF / WebP with JPG fallback) and art direction (serving different crops/aspect ratios per screen size).

```html
<picture>
  <!-- Format Negotiation: AVIF first (highest compression) -->
  <source srcset="/images/banner.avif" type="image/avif">
  
  <!-- Format Negotiation: WebP second -->
  <source srcset="/images/banner.webp" type="image/webp">
  
  <!-- Art Direction: Mobile aspect ratio -->
  <source media="(max-width: 600px)" srcset="/images/banner-mobile.jpg">
  
  <!-- Fallback default img (Always mandatory inside <picture>) -->
  <img 
    src="/images/banner.jpg" 
    alt="Autumn release promotional banner"
    width="1200" 
    height="600"
    loading="lazy"
  >
</picture>
```

---

## 3. Audio & Video Media

### The `<video>` Element

| Attribute | Purpose |
| :--- | :--- |
| **`controls`** | Shows standard playback controls (play, pause, volume, timeline). |
| **`autoplay`** | Begins playback automatically (must be paired with `muted` on most modern browsers). |
| **`muted`** | Mutes audio by default. Required for policy autoplay. |
| **`loop`** | Replays video continuously upon reaching the end. |
| **`poster`** | Image URL to display while video is downloading or until the user hits play. |
| **`playsinline`** | Plays inline on mobile screens instead of forcing fullscreen mode. |
| **`preload`** | `none` (saves bandwidth), `metadata` (loads dimensions and duration), `auto`. |

```html
<video 
  controls 
  preload="metadata" 
  poster="/media/preview-frame.jpg" 
  width="854" 
  height="480"
  playsinline
>
  <source src="/media/keynote.mp4" type="video/mp4">
  <source src="/media/keynote.webm" type="video/webm">
  
  <!-- Timed Subtitles / Captions (WebVTT format) -->
  <track 
    kind="captions" 
    src="/media/keynote-en.vtt" 
    srclang="en" 
    label="English Captions" 
    default
  >
  <track 
    kind="subtitles" 
    src="/media/keynote-es.vtt" 
    srclang="es" 
    label="Subtítulos en Español"
  >
  
  <p>Your browser does not support HTML5 video. <a href="/media/keynote.mp4">Download video</a>.</p>
</video>
```

### The `<audio>` Element

```html
<audio controls preload="metadata">
  <source src="/media/podcast-ep1.mp3" type="audio/mpeg">
  <source src="/media/podcast-ep1.ogg" type="audio/ogg">
  <p>Your browser does not support the audio tag.</p>
</audio>
```

---

## 4. Embedded Content (`<iframe>`, `<svg>`, `<canvas>`)

### Secure `<iframe>` Integration

The `<iframe>` element embeds another HTML document inside the current page.

```html
<iframe 
  src="https://maps.example.com/embed" 
  title="Office Location Map" 
  width="600" 
  height="450" 
  loading="lazy"
  referrerpolicy="strict-origin-when-cross-origin"
  sandbox="allow-scripts allow-same-origin allow-popups"
  allow="geolocation; fullscreen"
></iframe>
```

> [!IMPORTANT]
> - Always supply an accessible `title` attribute on every `<iframe>`.
> - Use the `sandbox` attribute to restrict untrusted embedded documents (disables plugins, forms, scripts unless explicitly allowed).

### Vector Graphics (`<svg>`) & Scriptable Canvas (`<canvas>`)

```html
<!-- Inline Scalable Vector Graphics -->
<svg viewBox="0 0 24 24" width="24" height="24" fill="currentColor" aria-hidden="true">
  <path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/>
</svg>

<!-- Canvas Drawing Element (Scriptable via 2D or WebGL context) -->
<canvas id="game-viewport" width="800" height="600">
  Fallback content shown if browser lacks canvas support.
</canvas>
```

# Kinetic Word Effects Editor

A responsive, single-file HTML text editor that automatically applies animated visual and particle effects to recognized keywords as the user types.

The editor is designed for desktop browsers and iPhone Safari. It uses a native `<textarea>` for reliable typing, caret movement, selection, deletion, autocorrect, and software-keyboard behavior. A synchronized visual system reproduces the text layout and renders animated keyword effects without allowing those effects to alter line wrapping.

## Features

- Large editable text area with live keyword detection
- Sixteen built-in animated word effects
- Native `<textarea>` input for stable editing behavior
- Layout-safe effect rendering that does not control line wrapping
- Correct alignment around punctuation and long unbroken strings
- Responsive full-window layout with no page-level scrollbars
- One vertical scrollbar inside the editor only
- Horizontal scrolling permanently disabled
- Visual effects clipped so outward particles cannot create horizontal overflow
- iPhone safe-area, dynamic viewport, keyboard, and orientation support
- Pinch zoom and double-tap zoom disabled in the mobile build
- Reduced particle density on compact and coarse-pointer devices
- Pause and resume controls for animations
- Restore-sample and clear-text controls
- Plain-text export
- Google Fonts integration:
  - **Inter** for the user interface
  - **Noticia Text** for editor text and effect words

## Supported Keywords

Keyword matching is case-insensitive and applies only to complete words.

| Keyword | Visual treatment |
| --- | --- |
| `Fire` | Flame gradient, glow, flicker, and rising embers |
| `Smoke` | Layered haze, drifting smoke particles, and soft ghosting |
| `metal` | Chrome gradient, moving reflection, and sparks |
| `wind` | Swaying text, motion trails, and air streaks |
| `Water` | Flowing blue gradient, bubbles, and ripple motion |
| `Ice` | Frosted coloring, glints, and floating ice crystals |
| `Electric` | Bright electric glow, jitter, and animated arcs |
| `Magic` | Purple-blue shimmer, stars, and sparkling particles |
| `Glitch` | RGB separation, sliced text, and digital noise blocks |
| `Neon` | Neon gradient, glow, flicker, and light particles |
| `Snow` | Cool white glow and falling snow particles |
| `Rain` | Wet blue styling and animated rain streaks |
| `Lava` | Hot molten gradient, pulsing glow, and rising bubbles |
| `Love` | Pink gradient, heartbeat scaling, and floating hearts |
| `Music` | Rhythmic bounce and floating musical notes |
| `Pixel` | Stepped movement and pixel-block particles |

## Getting Started

No installation or build process is required.

1. Open the Kinetic Word Effects Editor in a modern web browser.
2. Type or paste text into the editor.
3. Enter any supported keyword to trigger its effect.

Example:

```text
Fire rises through the Smoke while Electric light reflects from the metal walls.
```

## Controls

- **Restore sample** — Restores the built-in demonstration text.
- **Pause effects** — Hides animated effects while keeping the underlying text visible and editable.
- **Clear** — Removes all text from the editor.
- **Export .txt** — Exports the current content as a plain-text file. On supported iPhones, the system share interface may be used.

## How It Works

The editor separates text input, text layout, and animation rendering.

### 1. Native input layer

A transparent native `<textarea>` handles:

- Typing and deletion
- Caret positioning
- Text selection
- Autocorrect and composition input
- Software-keyboard interaction
- The editor's vertical scrollbar

The textarea remains the source of truth for all text.

### 2. Layout flow layer

A visible synchronized text-flow container reproduces the textarea's text using the same typography, padding, width, wrapping rules, and scroll position.

Recognized keywords are represented by normal inline marker spans. These markers remain part of the ordinary text flow, so they do not behave like indivisible `inline-block` elements and do not independently change wrapping around punctuation or long strings.

The marker text is visually hidden while still occupying its exact natural layout space.

### 3. Effect canvas

Animated copies of recognized keywords are placed in an absolutely positioned effect canvas. Their positions are measured from the inline markers after browser layout has completed.

Particles, glow, scaling, shaking, and outward motion occur only in this effect canvas. Because the effect canvas does not participate in text flow, animation cannot move adjacent text, change line breaks, or create a horizontally scrollable text region.

## Alignment Requirements

The textarea and layout flow layer must use exactly the same text metrics. The current version shares these values:

```css
font-family: "Noticia Text", Georgia, "Times New Roman", serif;
font-size: var(--font-size);
font-weight: 400;
line-height: var(--line-height);
letter-spacing: var(--letter-spacing);
white-space: pre-wrap;
overflow-wrap: anywhere;
word-break: break-word;
font-kerning: none;
font-variant-ligatures: none;
font-feature-settings: "kern" 0, "liga" 0;
```

Kerning and ligatures are disabled in the editor layers to reduce browser-dependent width differences between the native textarea and the HTML text-flow layer.

Changing typography on only one layer can cause visible text, selection, caret position, or line wrapping to become misaligned.

## Scrolling Behavior

The page is locked to the browser viewport:

- `html` and `body` do not scroll horizontally or vertically.
- The editor automatically fills the remaining window space.
- Only the native textarea scrolls vertically.
- The synchronized visual layers follow the textarea's vertical scroll position.
- Horizontal overflow is clipped at the page, editor shell, editor stage, display layer, and effect canvas levels.
- The script continuously resets horizontal scroll positions to zero.

Effects such as `wind` and `Smoke` may visually extend past a word, but those particles are clipped and cannot create a horizontal scrollbar.

## iPhone and Mobile Support

The HTML includes mobile-specific behavior for Safari and other touch browsers:

- `viewport-fit=cover` for notched and Dynamic Island devices
- Safe-area padding through `env(safe-area-inset-*)`
- Dynamic height updates using `window.visualViewport`
- Layout adjustment when the software keyboard opens
- Compact controls and a horizontally swipeable keyword legend
- Touch targets sized for mobile interaction
- Reduced particle counts on compact or coarse-pointer devices
- Disabled pinch-to-zoom and double-tap page zoom
- Disabled body overscroll and page-level scrolling
- One-finger vertical scrolling inside the editor
- Portrait and landscape layout support

## Fonts

The page imports these fonts from Google Fonts:

- [Inter](https://fonts.google.com/specimen/Inter)
- [Noticia Text](https://fonts.google.com/specimen/Noticia+Text)

**Inter** is used for headings, buttons, labels, status text, and other interface elements.

**Noticia Text** is used by the native textarea, synchronized text-flow layer, keyword markers, and animated effect words.

An internet connection is required for the Google-hosted fonts to load. If they are unavailable, the browser falls back to the fonts defined in the CSS.

Because text alignment depends on matching font metrics, the textarea and visual layers must always use the same fallback font list as well as the same primary font.

## Browser Compatibility

Recommended browsers:

- Google Chrome
- Microsoft Edge
- Safari on macOS
- Safari on iPhone
- Firefox

The editor depends on modern browser features, including:

- CSS custom properties
- CSS gradients, filters, clipping, and animations
- `requestAnimationFrame`
- `Element.getClientRects()`
- `window.visualViewport`
- Safe-area environment variables
- Pointer and touch events

Very old browsers may not render every effect or mobile behavior correctly.

## Customization

### Change the sample text

Find the `sampleText` constant in the script:

```javascript
const sampleText = `Your sample text here.`;
```

### Add a new keyword

A new effect generally requires four changes:

1. Add an entry to `effectConfig`.
2. Add a CSS class such as `.effect-thunder`.
3. Add any required particle setup inside `makeParticle()`.
4. Add the corresponding `@keyframes` animations.

Example:

```javascript
thunder: {
  className: 'effect-thunder',
  count: 8
}
```

The keyword regular expression is built automatically from the keys in `effectConfig`, so no separate keyword list needs to be maintained.

### Change editor typography

Update the shared rule applied to both `#editor` and `#displayLayer`. Do not change only one of them.

Also verify that `#flowContent`, effect markers, and animated effect tokens inherit compatible typography.

### Adjust letter spacing

The project uses a shared custom property:

```css
--letter-spacing: 0.015em;
```

Changing this variable updates the interface and editor typography consistently. For exact caret alignment, verify the result in both desktop and iPhone Safari after making typography changes.

### Adjust particle density

Each effect has a `count` value in `effectConfig`:

```javascript
smoke: {
  className: 'effect-smoke',
  count: 15
}
```

Higher values create denser effects but require more rendering work. Compact devices automatically apply a reduced particle scale.

### Adjust viewport behavior

Mobile sizing is controlled partly through:

```css
--visual-vh: 100dvh;
```

JavaScript updates this value using `window.visualViewport` so the editor can fit above the iPhone software keyboard.

## Performance Notes

- Avoid extremely high particle counts when many effect words appear at once.
- Blur filters and large shadows are among the most expensive effects.
- Pause effects when animation is not needed.
- Test on a physical iPhone, especially with long documents or many repeated keywords.
- Text changes rebuild the layout markers and effect canvas.
- Vertical scrolling synchronizes existing visual layers without changing the source text.
- Mobile devices use fewer particles to reduce frame drops, heat, and battery usage.

## Accessibility Notes

The editor uses a real textarea with an accessible label, preserving keyboard and assistive-input behavior.

However, the mobile build intentionally disables page zoom. This can reduce accessibility for people who rely on pinch-to-zoom. Consider adding one of the following before public distribution:

- A font-size control
- An accessibility mode that restores page zoom
- A reduced-effects mode
- A high-contrast mode

The page respects `prefers-reduced-motion` by minimizing animation duration for users who enable reduced motion in their operating system.

## File Structure

The project is contained in one HTML file:

```text
kinetic_word_effects_editor_build14_wrap_alignment_fix.html
```

The file includes:

- HTML interface markup
- Responsive desktop and iPhone CSS
- Google Fonts imports
- Keyword and particle animations
- Native textarea input logic
- Text-flow marker generation
- Absolute effect-canvas positioning
- Viewport and keyboard handling
- Zoom-prevention logic
- Vertical scroll synchronization
- Horizontal-overflow prevention
- Plain-text export functionality

No external JavaScript framework is required. Google Fonts are the only external resource.

## Deployment

The file can be:

- Opened directly from local storage
- Uploaded to a static web host
- Added to GitHub Pages
- Embedded in a larger website with additional integration work
- Saved to an iPhone and opened through a compatible browser or hosted URL

When hosting the page, use HTTPS so browser sharing, mobile APIs, and future web-platform features work reliably.

## License

No software license is included by default. Add an appropriate license before redistributing or publishing the project.

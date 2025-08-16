# Dark/Light Mode Standard
This document defines the single source of truth, utilities, component patterns, and testing practices for dark/light mode across the site.

## Source of Truth

* The authoritative state is on the `<html>` element:
  * `data-mode="light"` or `data-mode="dark"`
  * `.dark` class mirrors the same state for Tailwind v4 compatibility
* All mode-dependent CSS must target `html[data-mode="dark"]` and/or `html.dark`.
* Do not rely on `prefers-color-scheme` inside components. The system preference is used only to set an initial mode when no user preference is stored.

## Utilities

### `mode-switcher.js`

* File: `src/utils/mode-switcher.js`

#### Requirements

* On construction and on DOM ready, apply the current mode to `<html>`.
* Always set `data-mode` to an explicit value ("light" or "dark"). Never remove it.
* Keep `.dark` class exactly in sync with the mode.
* Persist user choice in `localStorage` under key `mode`.
* Dispatch a `mode-change` event with detail `{ mode: 'light' | 'dark' }`.

#### Recommended implementation pattern

```js
export class ModeSwitcher {
  constructor() {
    this.currentMode = this.getStoredMode() || this.getSystemPreference();
    this.applyMode(this.currentMode, true);
  }

  getStoredMode() {
    if (typeof window !== 'undefined') return localStorage.getItem('mode');
    return null;
  }

  getSystemPreference() {
    if (typeof window === 'undefined') return 'light';
    return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
  }

  storeMode(mode) {
    if (typeof window !== 'undefined') localStorage.setItem('mode', mode);
  }

  applyMode(mode, initialLoad = false) {
    if (typeof document === 'undefined') return;
    const html = document.documentElement;

    // Explicitly set data-mode to either 'light' or 'dark'
    html.setAttribute('data-mode', mode);
    html.classList.toggle('dark', mode === 'dark');

    if (!initialLoad) {
      this.currentMode = mode;
      this.storeMode(mode);
    }
    this.dispatchModeChange(mode);
  }

  dispatchModeChange(mode) {
    if (typeof window !== 'undefined') {
      window.dispatchEvent(new CustomEvent('mode-change', { detail: { mode } }));
    }
  }

  toggleMode() {
    const newMode = this.currentMode === 'light' ? 'dark' : 'light';
    this.applyMode(newMode);
    return newMode;
  }

  setMode(mode) {
    if (mode === 'light' || mode === 'dark') {
      this.applyMode(mode);
      return mode;
    }
    console.warn('Invalid mode:', mode);
    return this.currentMode;
  }

  getCurrentMode() { return this.currentMode; }
}

// Global instance
export const modeSwitcher = new ModeSwitcher();

if (typeof document !== 'undefined') {
  document.addEventListener('DOMContentLoaded', () => {
    modeSwitcher.applyMode(modeSwitcher.getStoredMode() || modeSwitcher.getSystemPreference());
  });
}

if (typeof window !== 'undefined') {
  window.modeSwitcher = window.modeSwitcher || modeSwitcher;
}
```

## Early Mode Application (FOUC Prevention)

* File: `src/layouts/BoilerPlateHTML.astro`

* Include an inline script before the module import to apply mode ASAP based on `localStorage` or system preference when no preference is stored.
* This script must set `data-mode` and `.dark` on `<html>` pre-paint.
* Example (already in place):

```html
<script is:inline>
  (function() {
    const savedMode = localStorage.getItem('mode');
    const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    const mode = savedMode || (systemPrefersDark ? 'dark' : 'light');
    const html = document.documentElement;

    // Explicitly set data-mode to either 'light' or 'dark'
    html.setAttribute('data-mode', mode);
    html.classList.toggle('dark', mode === 'dark');

    // Only react to system changes if no stored preference exists
    window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
      if (!localStorage.getItem('mode')) {
        const newMode = e.matches ? 'dark' : 'light';
        html.setAttribute('data-mode', newMode);
        html.classList.toggle('dark', newMode === 'dark');
      }
    });
  })();
</script>
<script type="module">
  import '/src/utils/mode-switcher.js';
</script>
```

## ModeToggle Component Pattern

* File: `src/components/ui/ModeToggle.astro`

### Standards

* Two static icons/images in the markup; visibility is controlled by global CSS.
* In light mode, show the moon (indicates you can switch to dark).
* In dark mode, show the sun (indicates you can switch to light).
* Button should inherit color via `text-foreground`.
* On click, call `window.modeSwitcher.toggleMode()` and update `aria-pressed`, `aria-label`, `title`.
* Listen for `mode-change` to sync all toggles.

### CSS (global)

```css
/* Default (light): show moon, hide sun */
.sun-icon { display: none; }
.moon-icon { display: block; }

/* Dark mode visibility */
html[data-mode="dark"] .sun-icon { display: block; }
html[data-mode="dark"] .moon-icon { display: none; }

/* Also support .dark */
html.dark .sun-icon { display: block; }
html.dark .moon-icon { display: none; }
```

### Script essentials

* Initialize from `modeSwitcher.getStoredMode() || modeSwitcher.getSystemPreference()`
* Update `aria-pressed`, `aria-label`, `title`
* Delegate click to `modeSwitcher.toggleMode()`
* Sync on `mode-change`

## Theming Components Pattern

### ThemeImage

* File: `src/components/ui/ThemeImage.astro`

* Provide both light and dark `<img>`.
* Use global selectors to toggle:

```css
/* Default */
.light-image { display: block; }
.dark-image { display: none; }

/* Dark mode */
html[data-mode="dark"] .light-image { display: none; }
html[data-mode="dark"] .dark-image { display: block; }

/* Tailwind dark class support */
html.dark .light-image { display: none; }
html.dark .dark-image { display: block; }
```

* Do not use Tailwind `dark:` utilities or `prefers-color-scheme` here.
* Sizing: the container controls size; images are `object-contain` and fill container as needed.

### SiteBrandMarkModeWrapper

* File: `src/components/ui/SiteBrandMarkModeWrapper.astro`

* Apply `className` to the wrapper via `class:list` (not on the `<img>`).
* Use high-specificity global CSS for `.light-logo` and `.dark-logo`. Keep `!important` only if conflicts persist; remove once stable:

```css
.brand-mark-wrapper .light-logo { display: block !important; }
.brand-mark-wrapper .dark-logo { display: none !important; }

html[data-mode="dark"] .brand-mark-wrapper .light-logo { display: none !important; }
html[data-mode="dark"] .brand-mark-wrapper .dark-logo { display: block !important; }

html.dark .brand-mark-wrapper .light-logo { display: none !important; }
html.dark .brand-mark-wrapper .dark-logo { display: block !important; }
```

## Global CSS Rules

* All dark/light swaps must be in `<style is:global>` blocks to avoid Astro style scoping conflicts.
* Only target `html[data-mode="dark"]` and `html.dark` as selectors.
* Avoid component-level `@media (prefers-color-scheme)` fallback. The global early script and mode-switcher manage system preference.

## Accessibility

* Toggle buttons must update:
  * `aria-pressed` to reflect dark mode boolean.
  * `aria-label` and `title` to "Switch to light/dark mode" accordingly.
* Ensure icons have `aria-hidden="true"` and actionable labels are on the button only.

## Performance

* Two-image pattern is acceptable for small logos/icons. For large media, consider `<picture>` with media queries if needed, but still control visibility via the same `html[data-mode]` pattern for consistency.
* Prevent FOUC with early inline script as shown.

## Testing and QA Checklist

* Header logo swaps correctly on click of ModeToggle.
* Footer logo swaps correctly.
* Toggle shows moon in light mode, sun in dark mode.
* `document.documentElement` contains both `data-mode` and `.dark` in sync at all times.
* Hard refresh: initial mode follows stored preference; otherwise follows system preference.
* System preference change while no stored preference exists updates mode live.
* `mode-change` event fires with `detail.mode` and other components respond (e.g., toggles update aria attributes).

## Maintenance Rules

* When creating any new component that varies by mode:
  * Add both light and dark elements in the DOM.
  * Add a global CSS block with the standard selectors.
  * Do not use Tailwind `dark:` or `prefers-color-scheme` in the component.
  * Ensure container sizing controls the asset scale; avoid forcing `w-full` `h-full` unless container is correct.
* When changing the mode-switcher:
  * Keep `data-mode` explicitly set to "light" or "dark".
  * Keep `.dark` class in sync.
  * Keep the `mode-change` event payload shape `{ mode }` stable.

By following this standard, the ModeToggle and all theme-aware components will switch reliably and consistently across the entire site without React/JSX or hydration overhead.
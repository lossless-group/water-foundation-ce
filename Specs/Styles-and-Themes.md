---
date_created: 2025-08-10
date_modified: 2025-08-11
title: Comprehensive Theming System for Tailwind CSS
lede: A scalable, client-specific theming system with dark/light mode support
date_authored_initial_draft: 2025-08-10
date_authored_current_draft: 2025-08-10
date_authored_final_draft: "[]"
date_first_published: "[]"
date_last_updated: "[]"
at_semantic_version: 0.0.0.1
publish: true
status: Draft
augmented_with: Windsurf Cascade on SWE-1
category: Atomic-Design
authors:
  - Michael Staton
site_uuid: 7bd4eb5f-80c2-4d43-a51e-df314d42fd4f
slug: water-foundation-styles-and-themes
---

# Design System Overview

A comprehensive theming system that supports multiple clients and color modes while maintaining consistency and scalability.

## Core Principles

1. **Client-First Architecture**: Design system that makes client-specific theming obvious and maintainable.
2. **Dark/Light Mode**: Built-in support for color schemes with system preference and manual override.
3. **Design Token Driven**: Use CSS custom properties for all theme values.
4. **Type Safety**: Leverage TypeScript for theme configuration and validation.
5. **Performance**: Critical CSS inlined, non-critical loaded asynchronously.

# Design Tokens

## Color System

### Base Color Scale

| full-text    | darkest  | darker  | dark   | base   | light  | lighter | lightest |
|--------------|----------|---------|--------|--------|--------|---------|----------|
| abbreviation| xxdk     | xdk     | dk     | base   | lt     | xlt     | xxlt     |
| Usage        | Text     | -       | -      | -      | -      | -       | BGs      |

### Semantic Color Roles

- **Primary**: Main brand color, used for primary actions and key elements
- **Secondary**: Secondary brand color, used for secondary actions and accents
- **Tertiary**: Additional brand color for specific UI elements
- **Accent**: Highlight color for important interactive elements
- **Background**: Background color for the page
- **Surface**: Background color for the content
- **Border**: Border color for the content
- **Emphasis**: Used to draw attention to important information
- **Warning**: Indicates caution or warning states
- **CTA**: Call-to-action elements that need to stand out
- **Legible**: Ensures text remains readable on any background

# Theme Architecture

## File Structure

```bash
src/

  styles/
    themes/
      base/               # Base design tokens
        colors.css        # Color definitions
        typography.css    # Font families and scales
        spacing.css       # Spacing scale
        breakpoints.css   # Responsive breakpoints
      clients/            # Client-specific overrides
        default/          # Default theme
          light.css       # Light mode variables
          dark.css        # Dark mode variables
        client1/          # Client 1 theme
          light.css
          dark.css
      components/         # Component-specific theming
        buttons.css
        cards.css
        forms.css
    utilities/           # Utility classes
      themes.css         # Theme switching utilities
      typography.css     # Text styles
    global.css           # Global styles and CSS resets
```

## Theme Configuration

Each theme is defined using TypeScript for type safety and better developer experience. The configuration includes both light and dark variants.

# Theme Implementation

## Theme Configuration (TypeScript)

The theme configuration uses TypeScript interfaces to ensure type safety and autocompletion:
```typescript
// src/styles/themes/config.ts

/**
 * Base color interface for theme colors
 */
interface ThemeColors {
  // Brand colors
  primary: string;
  secondary: string;
  tertiary: string;
  accent: string;
  
  // Functional colors
  success: string;
  warning: string;
  danger: string;
  info: string;
  
  // Neutral colors
  background: string;
  surface: string;
  border: string;
  
  // Text colors
  text: {
    primary: string;
    secondary: string;
    disabled: string;
    inverse: string;
  };
}

/**
 * Complete theme interface including both light and dark modes
 */
interface Theme {
  light: ThemeColors;
  dark: ThemeColors;
  typography?: {
    fontFamily: {
      sans: string;
      mono: string;
      display: string;
    };
  };
}

/**
 * Default theme configuration
 */
const defaultTheme: Theme = {
  light: {
    primary: '#684B9E',
    secondary: '#22A6B5',
    tertiary: '#F59C49',
    accent: '#4F46E5',
    success: '#10B981',
    warning: '#F59E0B',
    danger: '#EF4444',
    info: '#3B82F6',
    background: '#FFFFFF',
    surface: '#F9FAFB',
    border: '#E5E7EB',
    text: {
      primary: '#111827',
      secondary: '#4B5563',
      disabled: '#9CA3AF',
      inverse: '#FFFFFF',
    },
  },
  dark: {
    primary: '#8A6AE1',
    secondary: '#4ECDC4',
    tertiary: '#FFA94D',
    accent: '#818CF8',
    success: '#34D399',
    warning: '#FBBF24',
    danger: '#F87171',
    info: '#60A5FA',
    background: '#111827',
    surface: '#1F2937',
    border: '#374151',
    text: {
      primary: '#F9FAFB',
      secondary: '#D1D5DB',
      disabled: '#6B7280',
      inverse: '#111827',
    },
  },
  typography: {
    fontFamily: {
      sans: 'Inter, system-ui, sans-serif',
      mono: 'Fira Code, monospace',
      display: 'Inter, system-ui, sans-serif',
    },
  },
};

/**
 * Client-specific theme overrides
 */
const client1Theme: Theme = {
  ...defaultTheme,
  light: {
    ...defaultTheme.light,
    primary: '#4F46E5',
    secondary: '#10B981',
    accent: '#8B5CF6',
  },
  dark: {
    ...defaultTheme.dark,
    primary: '#818CF8',
    secondary: '#34D399',
    accent: '#A78BFA',
  },
};

/**
 * Export all available themes
 */
export const themes: Record<string, Theme> = {
  default: defaultTheme,
  client1: client1Theme,
  // Add more client themes here
};

/**
 * Get theme configuration for a specific client
 */
export function getTheme(clientId: string = 'default'): Theme {
  return themes[clientId] || defaultTheme;
}

/**
 * Generate CSS variables for a theme
 */
export function generateThemeVars(theme: Theme, mode: 'light' | 'dark' = 'light'): string {
  const colors = theme[mode];
  let cssVars = `:root[data-theme="${mode}"] {\n`;
  
  // Add color variables
  Object.entries(colors).forEach(([key, value]) => {
    if (typeof value === 'string') {
      cssVars += `  --color-${key}: ${value};\n`;
    } else if (typeof value === 'object' && value !== null) {
      // Handle nested objects (like text colors)
      Object.entries(value).forEach(([nestedKey, nestedValue]) => {
        cssVars += `  --color-${key}-${nestedKey}: ${nestedValue};\n`;
      });
    }
  });
  
  cssVars += '}';
  return cssVars;
}
```

# Implementation Status (August 2025)

## Actual Implementation Details

### What Was Built

The theme system was successfully implemented in `/home/mps/code/lossless-monorepo/astro-knots/twf-site/` with the following architecture:

#### File Structure (As Implemented)
```bash
src/
  styles/
    global.css           # Main CSS with Tailwind imports and dark mode overrides
    water-theme.css      # CSS custom properties for both themes
  utils/
    theme-switcher.js    # Theme toggle utility (default ↔ water)
    mode-switcher.js     # Mode toggle utility (light ↔ dark)
  pages/
    index.astro          # Demo page with toggle buttons
```

#### Theme System Architecture

**Two-Layer System:**
1. **Theme Layer**: `default` vs `water` (controlled by `data-theme="water"` attribute)
2. **Mode Layer**: `light` vs `dark` (controlled by `data-mode="dark"` attribute)

#### CSS Custom Properties Implementation

**water-theme.css** defines CSS custom properties for both themes:
- `:root` - Default theme colors (Tailwind defaults)
- `[data-theme="water"]` - Water theme colors (inverted/ocean blues)

**global.css** handles:
- Tailwind CSS imports
- Dark mode overrides using `[data-mode="dark"]` selectors
- CSS specificity fixes with `!important` declarations

#### JavaScript Utilities

**ThemeSwitcher Class:**
- Toggles between `default` and `water` themes
- Uses `data-theme` attribute on `<html>`
- Persists preference in localStorage
- Provides methods: `toggleTheme()`, `setTheme()`, `getCurrentTheme()`

**ModeSwitcher Class:**
- Toggles between `light` and `dark` modes
- Uses `data-mode` attribute on `<html>`
- Persists preference in localStorage
- Provides methods: `toggleMode()`, `setMode()`, `getCurrentMode()`

#### Integration with Astro

**CSS Import:**
```javascript
// In .astro frontmatter
import '../styles/global.css';
```

**JavaScript Integration:**
```javascript
import { themeSwitcher } from '../utils/theme-switcher.js';
import { modeSwitcher } from '../utils/mode-switcher.js';
```

### Key Implementation Challenges & Solutions

#### 1. CSS Specificity Issues
**Problem:** Tailwind utility classes weren't being overridden by dark mode styles.
**Solution:** Used `!important` declarations and specific selectors like `[data-mode="dark"] .bg-primary-500`.

#### 2. CSS Loading Order
**Problem:** CSS wasn't loading in Astro pages.
**Solution:** Explicit CSS import in Astro frontmatter: `import '../styles/global.css';`

#### 3. Button Visibility in Dark Mode
**Problem:** Dark mode CSS was making button text invisible.
**Solution:** Proper contrast handling with `[data-mode="dark"] .text-white` overrides.

### Testing Implementation

**Comprehensive Test Suite:**
- **33 passing tests** covering all functionality
- **Unit tests** for both ThemeSwitcher and ModeSwitcher classes
- **Integration tests** with JSDOM for DOM interactions
- **Vitest configuration** with proper setup files

**Test Files:**
- `src/utils/__tests__/theme-switcher.test.js`
- `src/utils/__tests__/mode-switcher.test.js`
- `src/utils/__tests__/toggle-integration.test.js`
- `vitest.config.js`

### Working Combinations

The system provides **4 distinct visual states:**
1. **Default + Light** - Standard Tailwind colors, light backgrounds
2. **Default + Dark** - Standard colors with dark backgrounds/light text
3. **Water + Light** - Ocean blue theme, light backgrounds
4. **Water + Dark** - Ocean blue theme with dark backgrounds/light text

### Usage Example

```html
<!-- Toggle buttons -->
<button id="theme-toggle">Toggle to Water Theme</button>
<button id="mode-toggle">Toggle to Dark Mode</button>

<script>
import { themeSwitcher } from '../utils/theme-switcher.js';
import { modeSwitcher } from '../utils/mode-switcher.js';

// Theme toggle
document.getElementById('theme-toggle').addEventListener('click', () => {
  themeSwitcher.toggleTheme();
});

// Mode toggle  
document.getElementById('mode-toggle').addEventListener('click', () => {
  modeSwitcher.toggleMode();
});
</script>
```

### Lessons Learned

1. **CSS Import Order Matters:** In Astro, CSS must be explicitly imported in frontmatter
2. **Specificity is Critical:** Dark mode overrides need `!important` to override Tailwind utilities
3. **Two-Layer Architecture Works:** Separating theme (colors) from mode (light/dark) provides flexibility
4. **localStorage Integration:** Persisting preferences enhances user experience
5. **Comprehensive Testing:** Both unit and integration tests are essential for theme systems

### Future Enhancements

- **System Preference Detection:** Auto-detect user's OS dark/light preference
- **Smooth Transitions:** Add CSS transitions between theme/mode changes
- **More Themes:** Extend beyond default/water to support multiple clients
- **Component-Level Theming:** Theme-aware component variants
Here’s a pragmatic pattern to stop button sprawl and standardize styling in Astro.

## Recommended Pattern

- Single canonical component: Use one `Button.astro` (you already have `src/components/basics/buttons/Button--Variants.astro` that’s close). Make this the only source of truth.
- Variants via props: Drive look/feel with typed props:
  - `intent`: `'primary' | 'secondary' | 'accent' | 'ghost' | 'link' | ...`
  - `variant`: `'solid' | 'transparent' | 'gradient' | 'lean' | ...`
  - `size`: `'sm' | 'md' | 'lg' | 'xl' | 'compact'`
  - `icon`, `external`, `className`
- Classes from maps: Compute classes via a single map/object per prop, then join. This is what `Button--Variants.astro` already does well.
- Content via slot: Prefer `<slot />` for label/content. Support an optional `text` prop for convenience, but normalize to slot internally.
- Design tokens: Reference theme tokens (Tailwind config or CSS vars) rather than hardcoding colors across multiple files.

## Why this works

- Consistency: One place to tweak colors, spacing, hover states.
- Discoverability: Fewer components; variants are explicit props.
- Type-safety: Union types keep variant space controlled.
- Styling flexibility: Consumers can still extend with `className`.

## How to apply here

- Promote `src/components/basics/buttons/Button--Variants.astro` to canonical “Button”.
- Option A: rename to `Button.astro` and export the API you want (`intent`/`variant`/`size`).
- Option B: keep file name, but treat it as canonical and export a stable prop interface.
- Deprecate wrappers `Button--Primary.astro`, `Button--Secondary.astro`, `Button--Accent.astro`:
  - Make them thin re-exports that call the canonical component with the right intent defaults.
  - Add a comment header “Deprecated: use Button with `intent='primary'`”.
  - This avoids breaking existing pages while guiding new usage.

## Suggested unified API

- File: `src/components/basics/buttons/Button.astro` (or keep your `Button--Variants.astro`)
- Props:
  - `href`: `string`
  - `intent?`: `'primary' | 'secondary' | 'accent' | 'ghost' | 'partner'`
  - `variant?`: `'solid' | 'transparent' | 'lean' | 'gradient' | 'text-gradient' | 'icon'`
  - `size?`: `'xs' | 'sm' | 'md' | 'lg' | 'xl' | 'compact'`
  - `icon?`: `'arrow' | 'plus' | 'external' | string`
  - `external?`: `boolean`
  - `className?`: `string`
  - Slot for label
- Implementation: mirror your existing `Button--Variants.astro` class maps; add an intent layer for color palette choices.

## How it plugs into the hero

- In `src/components/basics/HeroCenteredLogo--ImageFocused.astro`, map `targetActionsArray` to intent:
  - Think with Us → intent: `'accent'`
  - Invest with Us → intent: `'primary'`
  - Partner with Us → intent: `'secondary'`
- Render the canonical Button:

```astro
<Button href={...} intent="accent" size="md" className="w-full">Think with Us</Button>
```

## Migration plan

- Phase 1: Keep current files. Update hero to use canonical Button for all types.
- Phase 2: Change old wrappers to warn in dev (`console.warn`) and re-export canonical.
- Phase 3: Remove wrappers once codebase is migrated.

## Extras

- ARIA/External: Add `aria-label` option and automatic `target/_blank` `rel` for external.
- Testing: Snapshot a stories grid page showing all variants and sizes for visual QA.
- Docs: Create a small MDX or README documenting allowed prop values and examples.
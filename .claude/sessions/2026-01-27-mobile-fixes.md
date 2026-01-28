# Session: Mobile Formatting Fixes

**Date:** 2026-01-27

## Problem
Mobile formatting was broken:
- Blog pages appeared zoomed out
- No side padding on index and blog pages
- Blog titles overflowing instead of wrapping
- Favicon not updating (cache issue)

## Root Cause
Astro scopes CSS with attribute selectors like `[data-astro-cid-xxx]`. The universal reset `* { padding: 0 }` became `[data-astro-cid-xxx] { padding: 0 }` which has **higher specificity** (0,1,0) than element selectors like `body { padding: 20px }` (0,0,1). The reset was winning.

## Fixes Applied

### 1. Viewport Meta Tag
Added `initial-scale=1` to all pages:
```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

### 2. CSS Reset Fix
Removed `padding: 0` from the universal reset to avoid specificity conflicts:
```css
* {
  margin: 0;
  box-sizing: border-box;
  /* removed: padding: 0 */
}
```

### 3. Explicit Padding
- Body padding: `20px` on index and blog pages
- Main padding: `3rem 2rem 6rem` on article pages (20px sides on mobile)
- Added `padding: 0` specifically to `.posts` ul element

### 4. Blog Title Wrapping
Changed mobile styles to wrap instead of truncate:
```css
.post a {
  white-space: normal;
  overflow: visible;
}
```

### 5. Header Consistency
Made blog page header match article page:
- Margin: 4rem (3rem on mobile)
- Font size: 0.875rem
- Letter spacing: 0.01em

## Key Learning
When using Astro (or any framework with scoped CSS), be careful with universal selectors in resets. The scoping mechanism can give them unexpectedly high specificity.

## Files Changed
- `src/pages/index.astro`
- `src/pages/writing/index.astro`
- `src/pages/writing/[id].astro`

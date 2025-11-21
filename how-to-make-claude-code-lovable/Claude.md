# Claude Code Technical Guidelines

## Design System Access

**Claude automatically reads `design_brief.md`** when working on UI/styling tasks.

**No manual invocation needed** - just ask Claude to create or style components.

**When Claude uses design_brief.md:**
- Creating new UI components or pages
- Styling existing components
- Making visual/layout decisions
- Choosing colors, spacing, typography
- Implementing responsive behavior

**Claude skips design_brief.md for:**
- Bug fixes (unless styling-related)
- Backend logic, API routes, database queries
- Business logic or data processing
- Configuration changes
- Dependency updates
- Code refactoring (non-visual)
- Writing tests

---

## Tailwind CSS v4 Setup

### Required Configuration

**1. `postcss.config.mjs`**
```javascript
export default {
  plugins: {
    '@tailwindcss/postcss': {},
  },
};
```

**2. `globals.css`**
```css
@import "tailwindcss";

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 0 0% 3.9%;
    /* Use HSL format for color variables */
  }
}
```

**3. `tailwind.config.ts` (optional, required only for plugins)**
```typescript
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  // plugins: [require("tailwindcss-animate")], // Add if needed
};
export default config;
```

### Quick v4 Reference

**Renamed utilities:**
- `shadow-sm` → `shadow-xs`
- `blur-sm` → `blur-xs` 
- `rounded-sm` → `rounded-xs`
- `outline-none` → `outline-hidden`

**Removed utilities (use modern syntax):**
- `bg-opacity-50` → `bg-black/50`
- `flex-shrink-*` → `shrink-*`
- `flex-grow-*` → `grow-*`

**CSS variables:**
```html
<!-- v4 syntax -->
<div class="bg-(--brand-color)"></div>
```

---

## Global CSS Guidelines

Keep `globals.css` minimal. Use Tailwind utilities in components for most styling.

**globals.css should only contain:**
1. Base & reset styles
2. Typography setup
3. CSS variable definitions
4. Global page styles (body background, etc.)
5. Minimal reusable patterns via `@apply`
6. Third-party/utility styles

**Rule of thumb:**
- One-off styles → Tailwind utilities in `.tsx` files
- Global/brand-defining → `globals.css`

---

## Verification Checklist

After setup, verify:
- ✅ `postcss.config.mjs` uses `@tailwindcss/postcss`
- ✅ `globals.css` starts with `@import "tailwindcss"`
- ✅ No `@tailwind base/components/utilities` directives (v3 syntax)
- ✅ Color variables use HSL format
- ✅ Using v4 utility names (shadow-xs, not shadow-sm)
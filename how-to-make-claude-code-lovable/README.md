# Design System for Claude Code

Build beautiful, responsive websites with Tailwind v4 automatically.

---

## What This Does

Makes Claude Code automatically generate production-ready components with consistent styling, responsive design, and accessibility built-in.

**Before:** Spend hours styling components manually  
**After:** Claude automatically applies your design system to every component

---

## Setup (1 minute)

### Step 1: Add Files to Your Project

Copy these two files to your project root:

1. **`claude.md`** - Technical Tailwind v4 setup
2. **`design_brief.md`** - Your design system

That's it! Claude Code will automatically read these files.

### Step 2: Customize for Your Brand (Optional)

Edit `design_brief.md` to match your brand:

```markdown
## Brand Colors
Primary gradient: `from-emerald-600 to-teal-600`
Accent: `text-amber-500`
Background: `bg-slate-50`

## Typography
Headings: "Cabinet Grotesk", sans-serif
Body: "Inter", sans-serif
```

### Step 3: Start Building

Just ask Claude to create components - no special commands needed:

```
Create a hero section with gradient background
Style this button component
Build a pricing table with 3 tiers
```

Claude automatically applies your design system! ✅

---

## Usage

No special invocation needed. Just build:

```
Create a features section with 3 columns
Create a pricing table with 3 tiers
Create an FAQ accordion
Style this button component
```

Claude automatically uses your design system from `design_brief.md`.

---

## What You Get

- ✅ **Automatic** - No manual invocation needed
- ✅ **Tailwind v4** best practices
- ✅ **Mobile-first** responsive design
- ✅ **Accessible** components (WCAG AA)
- ✅ **Consistent** styling across all components
- ✅ **Version controlled** in your repo

### Included Components

- Hero sections
- Feature grids
- Testimonials
- Pricing tables
- FAQ accordions
- Buttons, cards, forms, and more

---

## Customization

### Brand Colors

Edit `design_brief.md`:

```markdown
## Brand Colors
Primary gradient: `from-rose-600 to-orange-500`
Secondary: `from-slate-700 to-slate-900`
Accent: `text-cyan-400`
```

### Typography

```markdown
## Typography
Headings: "Space Grotesk", sans-serif
Body: "Inter", sans-serif
Code: "JetBrains Mono", monospace
```

### Custom Patterns

Add your own component patterns to `design_brief.md`:

```markdown
## Custom Components

### Newsletter Section
[Your component code and guidelines]

### Product Card
[Your component code and guidelines]
```

---

## Files

- **README.md** - This guide
- **claude.md** - Technical Tailwind v4 setup
- **design_brief.md** - Your design system

Add both `claude.md` and `design_brief.md` to every project.

---

## Troubleshooting

**Design system not being applied?**  
Verify `design_brief.md` exists in your project root.

**Styles not applying?**  
Check that your project uses Tailwind v4 (`package.json` should show `"tailwindcss": "^4.x.x"`).

**Want different styling?**  
Edit `design_brief.md` with your brand colors and patterns.

---

## Architecture

```
Your Request
    ↓
claude.md (Technical setup - always loaded)
    ↓
design_brief.md (Design system - automatically loaded for UI work)
    ↓
Beautiful Component ✨
```

**Automatic:** Claude reads your design system from `design_brief.md` whenever you're building UI.

**Project-specific:** Each project has its own `design_brief.md` with custom brand colors and patterns.

---

That's it. Simple, automatic, effective.

Just ask Claude to build components - the design system is applied automatically.
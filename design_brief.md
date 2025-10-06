# Technical Requirements

## Tailwind CSS Version

**This design brief assumes Tailwind CSS v3.4.x** - all class names, configuration patterns, and examples reference Tailwind v3.

### Before Starting

Verify your project uses Tailwind v3:
- Check `package.json`: `"tailwindcss": "^3.4.x"`
- Verify `tailwind.config.ts` exists (v3 requires this file)
- Confirm `globals.css` starts with `@tailwind base;` NOT `@import "tailwindcss"`

If Tailwind v4 is detected, downgrade to v3 before proceeding:
```bash
npm uninstall tailwindcss @tailwindcss/postcss tw-animate-css
npm install -D tailwindcss@^3.4.0 postcss autoprefixer
```

### Configuration Requirements

**Required: `tailwind.config.ts`**
```typescript
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
    },
    extend: {},
  },
  plugins: [],
};
export default config;
```

**Required: `globals.css` structure**
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 0 0% 3.9%;
    /* Use HSL format for color variables */
  }
}
```

---

## General Styling Principles

### 1. The Container Principle

* **Never use full width.** The primary content of the page must be contained within a centered container.

* On desktop, this container should have a `max-width` (e.g., `max-w-7xl` in Tailwind) and generous horizontal padding (`px-4` or `px-8`).

* On mobile, the container should be fluid but still have comfortable horizontal padding to prevent content from touching the screen edges (`px-4`).

### 2. Mobile-First & Responsive Design

* Design and build for mobile screens first. This means starting with mobile styles and then adding larger screen breakpoints (`sm:`, `md:`, `lg:`) to enhance the layout for desktops.

* Use responsive utility classes extensively for padding, margins, font sizes, and grid layouts.

* Example: `py-16 md:py-24` (more vertical space on desktop), `grid-cols-1 md:grid-cols-2` (single column on mobile, two on desktop).

### 3. Light Theme & Color Palette (Standard)

* The default background should be a light gray or white (`bg-gray-50` or `bg-white`).

* Text should be a dark gray or black (`text-gray-900` or `text-black`).

* **Gradients are key.** Use subtle, radial or linear gradients for hero sections and accent elements.

* The color palette should be professional and clean, using a single primary accent color.

### 3. Dark Theme & Color Palette (Optional)

* The default background should be a dark gray or black (`bg-gray-950` or similar).

* Text should be white or a light gray (`text-white` or `text-gray-200`).

* **Gradients are key.** Use subtle, radial or linear gradients as backgrounds for hero sections, cards, and accent elements.

* The color palette should be restrained, primarily using blues, purples, and pinks for highlights and accents, often in gradient form.

* Use a soft, non-white glow or shadow effect to make elements pop against the dark background.

### 4. Typography

* Use a clean, sans-serif font like Inter, Poppins, or a similar one.

* Heading fonts should be bold and prominent (`font-bold text-4xl sm:text-5xl lg:text-6xl`).

* Body text should be legible with sufficient line height and be a lighter shade than headings for hierarchy.

* Use a font-smoothing class (`antialiased`) for a cleaner look.

### 5. Buttons and Interactive Elements

* Buttons should have a slight gradient or glow effect.

* Use `shadcn/ui` buttons as a base (`Button`). Customize with gradients or shadows.

* Interactive elements should have clear hover and focus states, often with a subtle color change or scale effect.

* Use rounded corners on all elements—buttons, cards, forms, and images (`rounded-xl` or `rounded-2xl`).

## Typical Landing Page Sections

### 1. Hero Section

* **Structure:**

    * A prominent, large headline that is short and impactful.

    * A compelling sub-headline or description.

    * A primary call-to-action (CTA) button.

    * An optional secondary CTA (e.g., "Learn More" or a link).

    * A visual element, like an app screenshot, a 3D model, or an abstract graphic.

* **Styling:**

    * The entire section should have a light or dark, often gradient, background depending on the chosen theme.

    * Text should be centered on mobile and left-aligned in the container on desktop.

    * The visual element should be a key focal point.

### 2. Features Section

* **Structure:**

    * A section headline and sub-headline.

    * A grid of feature cards.

    * Each card should have an icon or small graphic, a title, and a brief description.

* **Styling:**

    * Cards should have a slightly different background color or a subtle border to stand out from the page.

    * Use a responsive grid (e.g., `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4`).

### 3. Testimonials Section

* **Structure:**

    * A section headline (e.g., "What Our Users Say").

    * A grid or carousel of testimonial cards.

    * Each card should include a user's name, their role or company, a profile picture (optional), and the testimonial text.

* **Styling:**

    * Cards should follow the same rounded theme as the feature cards.

    * The user's name and role should be styled differently to create hierarchy.

    * Testimonial text should be quoted or styled to stand out.

### 4. Pricing / Plan Comparison

* **Structure:**

    * A headline like "Pricing" or "Discover a plan for you."

    * A responsive grid of pricing cards.

    * Each card should detail the plan name, price, and a bulleted list of features.

* **Styling:**

    * One plan (usually the most popular) can be highlighted with a different background or a prominent border.

    * Use `shadcn/ui` components for buttons and lists.

    * Make sure the features list is clear and easy to read.

### 5. FAQs Section

* **Structure:**

    * A headline for the section.

    * A series of accordion-style questions and answers.

* **Styling:**

    * Use `shadcn/ui`'s `Accordion` component as a base.

    * The questions should be clear, and the answers should appear smoothly upon clicking.

## Key CSS / Tailwind Classes to Leverage

* `container`: To center the content and apply a max-width.

* `px-4 md:px-8`: For consistent horizontal padding.

* `bg-gray-50` or `bg-white`: For a light background.

* `bg-gray-950`: For the dark background.

* `bg-gradient-to-br from-blue-500 to-cyan-500`: For light theme gradients.

* `bg-gradient-to-br from-purple-500 to-pink-500`: For dark theme gradients.

* `text-gray-900` or `text-black`: For text in the light theme.

* `text-white text-gray-200`: For text in the dark theme.

* `rounded-2xl`: For rounded corners on everything.

* `shadow-lg`: To add depth to cards and sections.

* `transition-all duration-300`: For smooth hover effects.

* `flex items-center justify-center`: For centering elements.

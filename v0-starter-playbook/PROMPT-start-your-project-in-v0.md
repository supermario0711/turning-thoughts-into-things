# Build the Initial Version of the Application (v0.dev + Next.js Edition)

## Step 1: Read All Documentation First

Before generating any code, read and analyze these markdown files from the GitHub repository:

- **vision.md** – Project goals, target audience, core experience
- **app-flow-and-roles.md** – User journeys and workflows
- **pages.md** – All route specifications and layouts
- **project-design.md** – Complete project-specific design system (colors, typography, spacing, visual style)
- **backend.md** – Data model and Supabase setup

After reading, briefly confirm:
- What this application does
- Who the users are
- What routes you'll build
- The design theme and approach
- The data model and Supabase configuration

---

## Step 2: Build in This Order

### Phase 1: Design System Setup

- Read **project-design.md** thoroughly
- Set up Next.js font loading using `next/font` with specified fonts
- Configure Tailwind 4 in `tailwind.config.ts` with exact colors from the color palette
- Set up typography (fonts, weights, sizes) following the type scale
- Note the spacing scale and component style specifications
- All styling should follow project-design.md specifications

**Font Loading:**
```typescript
// app/layout.tsx
import { Inter, Poppins } from 'next/font/google'

const inter = Inter({ 
  subsets: ['latin'],
  variable: '--font-inter',
  display: 'swap',
})

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className={inter.variable}>{children}</body>
    </html>
  )
}
```

**Tailwind Config:**
```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Use exact colors from project-design.md
        primary: {
          DEFAULT: '#HEX',
          light: '#HEX',
          dark: '#HEX',
        },
        accent: {
          DEFAULT: '#HEX',
          light: '#HEX',
        },
      },
      fontFamily: {
        sans: ['var(--font-inter)', 'sans-serif'],
      },
      spacing: {
        // Add spacing scale from project-design.md
      },
    },
  },
  plugins: [],
}

export default config
```

### Phase 2: Supabase Setup

- Follow the exact setup from **backend.md**:
  - Install `@supabase/supabase-js` and `@supabase/ssr`
  - Create environment variables file (`.env.local`)
  - Set up server-side Supabase client (`lib/supabase/server.ts`)
  - Set up client-side Supabase client (`lib/supabase/client.ts`)
- Create TypeScript types based on the data model in backend.md
- DO NOT implement authentication unless explicitly specified
- Focus on minimal data structure needed for core functionality

**Implementation:**
```typescript
// lib/supabase/server.ts
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export function createClient() {
  const cookieStore = cookies()
  
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        get(name: string) {
          return cookieStore.get(name)?.value
        },
      },
    }
  )
}
```

```typescript
// lib/supabase/client.ts
import { createBrowserClient } from '@supabase/ssr'

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

```typescript
// types/database.ts
// Types from backend.md data model
export type [Entity] = {
  id: string
  [field]: [type]
  created_at: string
  updated_at: string
}
```

### Phase 3: Database Schema Setup

- Provide the SQL migration script from **backend.md** for the user to run in Supabase
- Include table creation, RLS policies, and indexes
- Create seed data if specified in backend.md

**Provide this to the user:**
```markdown
## Database Setup Instructions

Run this SQL in your Supabase SQL Editor:

```sql
-- [SQL from backend.md]
CREATE TABLE [table_name] (...);
ALTER TABLE [table_name] ENABLE ROW LEVEL SECURITY;
CREATE POLICY ...;
```

After running the SQL, add these environment variables to `.env.local`:
```env
NEXT_PUBLIC_SUPABASE_URL=your_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```
```

### Phase 4: Build All Routes

- Build every route from **pages.md** in order of user flow
- Follow Next.js App Router file structure:
  - `app/page.tsx` for main route
  - `app/[route]/page.tsx` for other routes
- **Choose the right component type:**
  - **Server Component (default)**: Data fetching, static content, SEO
  - **Client Component**: Forms, interactivity, hooks, browser APIs
- Follow layout specs and content sections exactly from pages.md
- Apply styles from **project-design.md** (colors, typography, spacing, component styles)
- Make it responsive (mobile-first approach)
- Apply design system consistently across all routes

**Server Component Pattern (default - no 'use client'):**
```typescript
// app/page.tsx
import { createClient } from '@/lib/supabase/server'

export default async function Page() {
  const supabase = createClient()
  const { data, error } = await supabase.from('table').select()
  
  if (error) return <div>Error loading data</div>
  
  return (
    <div className="max-w-7xl mx-auto px-4">
      {/* Render data using project-design.md styles */}
    </div>
  )
}
```

**Client Component Pattern (when interactivity needed):**
```typescript
// components/InteractiveForm.tsx
'use client'

import { useState } from 'react'
import { createClient } from '@/lib/supabase/client'

export default function InteractiveForm() {
  const [loading, setLoading] = useState(false)
  const supabase = createClient()
  
  async function handleSubmit(e: React.FormEvent) {
    e.preventDefault()
    setLoading(true)
    // Handle submission
    setLoading(false)
  }
  
  return <form onSubmit={handleSubmit}>{/* Form content */}</form>
}
```

**Hybrid Pattern (Server Component with Client islands):**
```typescript
// app/page.tsx - Server Component
import { createClient } from '@/lib/supabase/server'
import InteractiveForm from '@/components/InteractiveForm'

export default async function Page() {
  const supabase = createClient()
  const { data } = await supabase.from('table').select()
  
  return (
    <div>
      {/* Static content rendered on server */}
      <h1>Static Data</h1>
      
      {/* Interactive form as Client Component island */}
      <InteractiveForm initialData={data} />
    </div>
  )
}
```

### Phase 5: Connect Data Layer

- Implement data fetching in Server Components
- Use Supabase client directly (NO Next.js API routes)
- For mutations, use Server Actions
- Add loading states using `loading.tsx` files or Suspense
- Add error states with user-friendly messages using `error.tsx` files
- Implement data flow based on backend.md architecture

**Data Patterns:**

**Reading (Server Component):**
```typescript
// app/page.tsx
import { createClient } from '@/lib/supabase/server'

export default async function Page() {
  const supabase = createClient()
  
  const { data, error } = await supabase
    .from('[table]')
    .select('*')
    .order('created_at', { ascending: false })
  
  if (error) return <div>Error loading data</div>
  
  return <div>{/* Render data */}</div>
}
```

**Writing (Server Action):**
```typescript
// app/actions.ts
'use server'

import { createClient } from '@/lib/supabase/server'
import { revalidatePath } from 'next/cache'

export async function createItem(formData: FormData) {
  const supabase = createClient()
  
  const { data, error } = await supabase
    .from('[table]')
    .insert({
      [field]: formData.get('[field]'),
    })
    .select()
    .single()
  
  if (error) return { error: error.message }
  
  revalidatePath('/') // Refresh the page data
  return { data }
}
```

**Client-Side Interaction:**
```typescript
'use client'

import { createClient } from '@/lib/supabase/client'
import { useState } from 'react'

export function Component() {
  const [loading, setLoading] = useState(false)
  const supabase = createClient()
  
  async function handleAction() {
    setLoading(true)
    const { error } = await supabase
      .from('[table]')
      .update({ [field]: value })
      .eq('id', id)
    
    setLoading(false)
  }
  
  return <button onClick={handleAction}>Action</button>
}
```

### Phase 6: User Flows & Navigation

- Implement complete workflows from **app-flow-and-roles.md**
- Use Next.js `Link` component for navigation between routes
- Add form validation where needed (HTML5 + client-side)
- Add proper error handling with user-friendly messages
- Add loading states using Suspense or loading.tsx
- Ensure accessibility (semantic HTML, ARIA labels, keyboard navigation)
- Test complete user journeys
- Ensure the core experience from vision.md works seamlessly

**Navigation:**
```typescript
import Link from 'next/link'

export default function Nav() {
  return (
    <nav>
      <Link href="/" className="text-primary hover:text-primary-dark">
        Home
      </Link>
    </nav>
  )
}
```

**Error Handling:**
```typescript
'use client'

export default function Form() {
  const [error, setError] = useState<string | null>(null)
  
  async function handleSubmit() {
    try {
      const { error: supabaseError } = await supabase.from('table').insert(data)
      if (supabaseError) throw supabaseError
    } catch (err) {
      setError('Something went wrong. Please try again.')
    }
  }
  
  return (
    <>
      {error && (
        <div role="alert" className="text-red-500 text-sm">
          {error}
        </div>
      )}
      {/* Form */}
    </>
  )
}
```

**Loading States:**
```typescript
// app/loading.tsx
export default function Loading() {
  return <div className="animate-pulse">Loading...</div>
}

// Or with Suspense
import { Suspense } from 'react'

export default function Page() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <DataComponent />
    </Suspense>
  )
}
```

**Accessibility:**
```typescript
// Semantic HTML
<button type="submit" aria-label="Submit form">
  Submit
</button>

// Form validation
<input
  type="email"
  required
  aria-required="true"
  aria-invalid={error ? "true" : "false"}
  aria-describedby={error ? "email-error" : undefined}
/>
{error && <span id="email-error" role="alert">{error}</span>}
```

---

## Step 3: Implementation Requirements

### Design Adherence
- Use exact hex codes from project-design.md color palette in `tailwind.config.ts`
- Apply specified font families using `next/font`
- Follow the spacing scale for consistent padding/margins
- Use component styles as specified (buttons, cards, inputs)
- Implement visual effects (shadows, borders, gradients) as documented
- Use Tailwind 4 utility classes

### Data Handling
- Use Supabase client directly (NEVER Next.js API routes)
- Follow the exact data model from backend.md
- Implement proper error handling for Supabase operations
- Use Server Components for data fetching when possible
- Use Server Actions for mutations
- Use Client Components only when interactivity is required

### Code Quality
- Mobile-first responsive design using Tailwind breakpoints
- Semantic HTML for accessibility
- TypeScript with proper types from database schema
- Server Components by default, Client Components only when needed
- Proper loading and error states
- Clean component structure following Next.js App Router best practices

### Critical: What NOT to Build
- Do NOT add authentication/login unless explicitly in backend.md
- Do NOT add features beyond what's documented in pages.md
- Do NOT create Next.js API routes (use Supabase directly)
- Do NOT add routes not listed in pages.md
- Do NOT add landing pages, settings, or profile pages unless specified

---

## Step 4: Testing the Build

Before considering the build complete, verify:

1. **Core Experience:** The primary user action from vision.md works perfectly
2. **All Routes:** Every route from pages.md is built and functional
3. **User Flow:** Complete journey from app-flow-and-roles.md works end-to-end
4. **Design Consistency:** All elements match project-design.md specifications
5. **Data Persistence:** Supabase operations work correctly (read/write)
6. **Component Types:** Server Components used where possible, Client Components only when needed
7. **Responsive:** Application works on mobile, tablet, and desktop
8. **No Extra Features:** Nothing built that wasn't in the documentation
9. **No API Routes:** All backend logic uses Supabase directly or Edge Functions
10. **Database Setup:** SQL migration script provided for user to run

---

## Step 5: When Done

Provide:

1. **Build Summary:**
   - Routes built (list each one from pages.md)
   - Core features implemented
   - Component breakdown (Server vs Client Components)
   - Data operations implemented
   - Any notable implementation decisions

2. **Setup Instructions:**
   ```markdown
   ## Setup Instructions
   
   1. **Install Dependencies:**
      ```bash
      npm install
      ```
   
   2. **Set up Supabase:**
      - Create a project at [supabase.com](https://supabase.com)
      - Run the SQL migration script below in your Supabase SQL Editor
      - Copy your project URL and anon key
   
   3. **Configure Environment Variables:**
      Create `.env.local`:
      ```env
      NEXT_PUBLIC_SUPABASE_URL=your_project_url
      NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
      ```
   
   4. **Run Development Server:**
      ```bash
      npm run dev
      ```
   
   ## SQL Migration Script
   ```sql
   [SQL from backend.md]
   ```
   ```

3. **How to Test:**
   - Step-by-step instructions to test the core user journey
   - What data to expect (or how to add seed data)
   - How to trigger key interactions

4. **Scope Adherence:**
   - Confirm what was explicitly NOT built per documentation
   - Note any deviations (with reasons) from the documentation
   - Confirm no Next.js API routes were created

5. **File Structure Overview:**
   ```
   project/
   ├── app/
   │   ├── layout.tsx (Server Component)
   │   ├── page.tsx (Server Component)
   │   ├── [route]/
   │   │   └── page.tsx
   │   ├── actions.ts (Server Actions)
   │   └── components/
   │       └── [components]
   ├── lib/
   │   └── supabase/
   │       ├── server.ts
   │       └── client.ts
   ├── types/
   │   └── database.ts
   ├── tailwind.config.ts
   ├── .env.local (create this)
   └── package.json
   ```

---

## Quick Reference

### Files to Read (in order):
1. vision.md - Understand the "why"
2. app-flow-and-roles.md - Understand the user journey
3. pages.md - Understand what routes to build
4. project-design.md - Understand how it should look
5. backend.md - Understand Supabase setup and data model

### Build Order:
Design Setup → Supabase Setup → Database Schema → Build Routes → Connect Data → Test Flows

### Key Principles:
- **Stay strictly within documented scope** - If it's not in the docs, don't build it
- **Server Components first** - Use Client Components only when necessary
- **No API routes** - Use Supabase directly or Edge Functions
- **Mobile-first** - Design for mobile, then enhance for larger screens
- **Type safety** - Use TypeScript types from database schema

---

## Tech Stack Reminder

**Framework:** Next.js 14+ (App Router)
**Styling:** Tailwind CSS 4
**Components:** shadcn/ui (if needed)
**Backend:** Supabase (PostgreSQL + Edge Functions)
**Language:** TypeScript
**State:** React Server Components (default) + Client Components (when needed)

---

## Critical Reminders

1. **Read ALL markdown files before writing any code**
2. **Use Server Components by default**, Client Components only for interactivity
3. **Never create Next.js API routes** - use Supabase directly
4. **Follow project-design.md exactly** - colors, fonts, spacing, component styles
5. **Build only what's in pages.md** - no extra features
6. **Provide SQL migration script** for user to run in Supabase
7. **Use mobile-first responsive design** with Tailwind breakpoints
8. **Apply proper loading and error states** using `loading.tsx` and `error.tsx`
9. **Use TypeScript types** from the database schema
10. **Test the complete user flow** from app-flow-and-roles.md

---

Start by reading all the markdown files from the GitHub repository, then build the application following the phases above. Ask for clarification if any specifications are unclear or conflicting.
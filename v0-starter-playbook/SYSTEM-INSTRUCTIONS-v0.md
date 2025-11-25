# v0 System Instructions

## Workflow
**ALWAYS read markdown files from /docs folder FIRST:**
- `/docs/vision.md` - Goals and core feature
- `/docs/app-flow-and-roles.md` - User journeys  
- `/docs/pages.md` - Route specifications
- `/docs/project-design.md` - Design system
- `/docs/backend.md` - Supabase data model

**Before ANY code:** Summarize and confirm understanding.

## Validation (ENFORCE STRICTLY)
**Every code change MUST match /docs markdown files:**
- Colors: EXACT hex from project-design.md
- Routes: ONLY what's in pages.md
- Data: Exact tables/fields from backend.md
- Flows: From app-flow-and-roles.md

**When user requests ANYTHING not in docs:**
STOP. Ask: "This isn't in /docs/[file].md. Should I:
1. Keep current spec and skip this?
2. Update /docs/[file].md first to include it?"

Do NOT proceed until clarified.

**STOP and ask when:**
- Specs missing or conflicting
- User requests deviate from pages.md
- Data fields needed but not documented
- Design details ambiguous

## Supabase Security
**NEVER create API routes.** Use:
- Supabase client from Server Components
- Server Actions for mutations
- Edge Functions for complex logic

**Follow backend.md RLS policies:**
- MVP: Public access (permissive)
- Post-MVP: Auth-based with `auth.uid()`

**Env vars (never commit):**
```
NEXT_PUBLIC_SUPABASE_URL=...
NEXT_PUBLIC_SUPABASE_ANON_KEY=...
```

## Components
**Server Components default** for data/static content.
**Client Components (`'use client'`) ONLY for:**
- Forms/interactive UI
- React hooks
- Browser APIs

## Scope
**Build ONLY pages.md routes.** Never add:
- Auth/login pages
- Settings/profile pages
- Admin dashboards
- Search/filters/analytics

## Rules
1. Documentation in /docs = source of truth (CHECK EVERY TIME)
2. User deviates from docs = STOP and ask to clarify
3. Server Components first
4. No API routes
5. Exact design from project-design.md
6. Mobile-first
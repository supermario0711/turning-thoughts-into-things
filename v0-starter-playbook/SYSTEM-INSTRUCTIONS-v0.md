# v0 System Instructions

## Workflow
**Read markdown files from GitHub repo first:**
- `vision.md` - Goals and core feature
- `app-flow-and-roles.md` - User journeys  
- `pages.md` - Route specifications
- `project-design.md` - Design system
- `backend.md` - Supabase data model

**Before coding:** Summarize and confirm understanding.

## Validation
**Match every decision to markdown files:**
- Colors: EXACT hex from project-design.md
- Routes: ONLY what's in pages.md
- Data: Exact tables/fields from backend.md
- Flows: From app-flow-and-roles.md

**STOP and ask when:**
- Specs missing or conflicting
- User requests features not in pages.md
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

**User requests unlisted features?** Ask to update pages.md first.

## Rules
1. Documentation = source of truth
2. Ask when unclear
3. Server Components first
4. No API routes
5. Exact design from project-design.md
6. Mobile-first
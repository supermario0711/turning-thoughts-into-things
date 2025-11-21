# v0.dev MVP Workflow

## Quick Start Guide

### Step 1: Generate Markdown Files
Use the **PROMPT-generate-markdown-files-v0.md** prompt to create your project documentation.

**Optional:** Improve your project description first using [this guide](https://marioottmann.com/newsletters/how-to-save-time-and-money-when-building-with-lovable-21419296)

This generates 5 markdown files:
- `vision.md`
- `app-flow-and-roles.md`
- `pages.md`
- `project-design.md`
- `backend.md`

### Step 2: Update v0 System Instructions
1. Go to v0.dev → Settings → Custom Instructions
2. Paste contents of **SYSTEM-INSTRUCTIONS-v0.md**
3. Save

### Step 3: Start Your Project
Use the **PROMPT-start-your-project-in-v0.md** as your first message to v0

### Step 4: Upload Markdown Files
v0 will ask for the markdown files → Upload all 5 files

### Step 5: Move Docs to Repository
After v0 generates the first version:
- Ask v0: "Upload all markdown files to a `/docs` folder in the project"

### Step 6: Connect to GitHub
Push your code to GitHub and you're done!

---

## Files in This Package

- **SYSTEM-INSTRUCTIONS-v0.md** - v0 custom instructions (1,638 chars)
- **PROMPT-generate-markdown-files-v0.md** - Generate project docs
- **PROMPT-start-your-project-in-v0.md** - Build instruction prompt

## Tech Stack

- Next.js 14+ (App Router)
- Supabase (Database + Auth ready)
- Tailwind CSS 4
- TypeScript
- shadcn/ui
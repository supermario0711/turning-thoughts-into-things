# Complete Step-by-Step Guide: Migrating Lovable Projects to Independent Setup

This is a comprehensive, detailed guide for migrating any Lovable-managed project to a fully independent GitHub repository and Supabase project. Follow every step exactly as written.

## Overview

**What You'll Achieve:**
- ✅ Independent GitHub repository (no Lovable dependency)
- ✅ Modern Supabase CLI that works properly
- ✅ Clean migration history (fixing Lovable's broken naming conventions)
- ✅ Full control over deployments and database changes
- ✅ Local development environment

## Prerequisites Check

Before starting, ensure you have:
- [ ] GitHub account
- [ ] Supabase account
- [ ] Node.js/npm installed
- [ ] Docker installed (for local Supabase)
- [ ] Homebrew installed (macOS) or equivalent package manager

## Phase 1: Repository Migration

### Step 1: Create New GitHub Repository

1. **Log into GitHub**
   - Go to https://github.com
   - Click the "+" icon in top right
   - Select "New repository"

2. **Configure Repository**
   - Repository name: `your-project-name`
   - Set to **Private** (recommended)
   - Do NOT initialize with README, .gitignore, or license
   - Click "Create repository"

3. **Copy Repository URL**
   - Copy the HTTPS URL: `https://github.com/yourusername/your-project-name.git`

### Step 2: Clone and Update Repository

1. **Navigate to Your Coding Directory**
   ```bash
   cd /Users/yourusername/Coding
   # Replace with your actual coding directory path
   ```

2. **Check Current Directory Structure**
   ```bash
   ls -la
   # Verify you can see your existing Lovable project
   ```

3. **Clone Existing Project to New Directory**
   ```bash
   # If your Lovable project is called "my-lovable-app"
   cp -r my-lovable-app your-new-project-name
   cd your-new-project-name
   ```

4. **Check Git Status**
   ```bash
   git status
   # Verify you're in a git repository
   ```

5. **Check Current Remote**
   ```bash
   git remote -v
   # This will show the current Lovable remote
   ```

6. **Update Git Remote**
   ```bash
   git remote set-url origin https://github.com/yourusername/your-project-name.git
   # Replace with your actual GitHub repository URL
   ```

7. **Verify Remote Update**
   ```bash
   git remote -v
   # Should now show your GitHub repository
   ```

8. **Push to New Repository**
   ```bash
   git push -u origin main
   # This may take a few minutes
   ```

9. **Verify Push Success**
   - Go to your GitHub repository in browser
   - Confirm all files are present

## Phase 2: Supabase CLI Setup

### Step 3: Update Supabase CLI

1. **Check Current Version**
   ```bash
   supabase --version
   ```

2. **Update Supabase CLI (macOS with Homebrew)**
   ```bash
   brew upgrade supabase
   ```

3. **For Other Systems:**
   ```bash
   # Linux/WSL
   curl -fsSL https://raw.githubusercontent.com/supabase/cli/main/install.sh | sh
   
   # Windows (PowerShell)
   # Download from GitHub releases: https://github.com/supabase/cli/releases
   ```

4. **Verify Updated Version**
   ```bash
   supabase --version
   # Should show v2.33.9 or newer
   ```

5. **Check Login Status**
   ```bash
   supabase projects list
   # If not logged in, run: supabase login
   ```

### Step 4: Create New Supabase Project

1. **List Existing Projects (to get Organization ID)**
   ```bash
   supabase projects list
   ```
   - Note your ORG ID (format: `grxwqjxsvdhxqurhdaop`)

2. **Create New Project**
   ```bash
   supabase projects create "your-project-name" \
     --org-id YOUR_ORG_ID \
     --region eu-north-1 \
     --db-password YOUR_SECURE_PASSWORD
   ```
   
   **Important Notes:**
   - Replace `YOUR_ORG_ID` with your actual org ID
   - Choose region close to you: `us-east-1`, `eu-north-1`, etc.
   - Use a strong password you'll remember
   - Do NOT share your password

3. **Verify Project Creation**
   ```bash
   supabase projects list
   ```
   - Note your new project's REFERENCE ID (format: `cjfdbrcabvgdunrojxhx`)

## Phase 3: Migration Setup (Critical Phase)

### Step 5: Create Migration Workspace

1. **Create Temporary Directory**
   ```bash
   cd /Users/yourusername/Coding
   mkdir your-project-migration-temp
   cd your-project-migration-temp
   ```

2. **Initialize Fresh Supabase Project**
   ```bash
   supabase init
   ```
   - When prompted for VS Code settings: Type `N` and press Enter
   - When prompted for IntelliJ Settings: Type `N` and press Enter

3. **Link to New Supabase Project**
   ```bash
   supabase link --project-ref YOUR_NEW_PROJECT_REF
   ```
   - Replace `YOUR_NEW_PROJECT_REF` with your project's reference ID
   - Enter your database password when prompted

4. **Verify Linking**
   ```bash
   supabase status
   # Should show your new project details
   ```

### Step 6: Copy Original Project Files

1. **Create Directories**
   ```bash
   mkdir -p supabase/migrations
   mkdir -p supabase/functions
   ```

2. **Copy Migration Files**
   ```bash
   cp /Users/yourusername/Coding/your-new-project-name/supabase/migrations/* \
      supabase/migrations/
   ```

3. **Copy Edge Functions**
   ```bash
   cp -r /Users/yourusername/Coding/your-new-project-name/supabase/functions/* \
         supabase/functions/
   ```

4. **Verify Files Copied**
   ```bash
   ls -la supabase/migrations/
   ls -la supabase/functions/
   ```

### Step 7: Fix Migration Issues (The Critical Step)

**Problem Explanation**: Lovable creates migrations with UUID-based naming (like `20250625112310-f08312aa-defe-4cba-b3c2-aa4c004d8d03.sql`) that doesn't work with modern Supabase CLI, which expects format `YYYYMMDDHHMMSS_description.sql`.

1. **Remove Broken Migration Files**
   ```bash
   rm supabase/migrations/*.sql
   ```

2. **Create New Comprehensive Migration**
   ```bash
   supabase migration new initial_schema
   ```
   - This creates a file like `20250804204246_initial_schema.sql`

3. **Analyze Original Migrations**
   
   You need to read through ALL the original migration files and extract:
   - Table definitions
   - Constraints and indexes
   - RLS policies
   - Functions and triggers
   - Extensions

   ```bash
   # Go back to examine original files
   cd /Users/yourusername/Coding/your-new-project-name/supabase/migrations
   ls -la
   ```

4. **Read First Migration File**
   ```bash
   cat 20250625112310-*.sql
   # This usually contains the basic table structure
   ```

5. **Read All Migration Files Systematically**
   ```bash
   # Read each file to understand what it does
   for file in *.sql; do
     echo "=== $file ==="
     head -20 "$file"
     echo ""
   done
   ```

### Step 8: Create Comprehensive Migration

1. **Go Back to Migration Temp Directory**
   ```bash
   cd /Users/yourusername/Coding/your-project-migration-temp
   ```

2. **Find Your New Migration File**
   ```bash
   ls supabase/migrations/
   # Should show something like: 20250804204246_initial_schema.sql
   ```

3. **Open Migration File in Text Editor**
   ```bash
   # Use your preferred editor
   nano supabase/migrations/20250804204246_initial_schema.sql
   # or
   code supabase/migrations/20250804204246_initial_schema.sql
   ```

4. **Create Comprehensive Migration Content**

   **Template Structure** (fill in with your actual schema):
   ```sql
   -- Comprehensive Database Migration for [Your Project Name]
   -- This file contains the complete database schema
   
   -- Enable necessary extensions
   CREATE EXTENSION IF NOT EXISTS pg_cron;
   
   -- =============================================================================
   -- TABLES
   -- =============================================================================
   
   -- 1. Main tables (replace with your actual tables)
   CREATE TABLE public.your_main_table (
     id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
     user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE NOT NULL,
     name TEXT NOT NULL,
     created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
     updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
   );
   
   -- Add constraints
   ALTER TABLE public.your_main_table ADD CONSTRAINT valid_name_length 
   CHECK (char_length(name) <= 200 AND char_length(name) > 0);
   
   -- Create indexes
   CREATE INDEX idx_your_main_table_user_id ON public.your_main_table(user_id);
   
   -- =============================================================================
   -- FUNCTIONS
   -- =============================================================================
   
   -- Create functions (replace with your actual functions)
   CREATE OR REPLACE FUNCTION public.handle_new_user()
   RETURNS trigger
   LANGUAGE plpgsql
   SECURITY DEFINER SET search_path = public
   AS $$
   BEGIN
     -- Your function logic here
     RETURN NEW;
   END;
   $$;
   
   -- =============================================================================
   -- TRIGGERS
   -- =============================================================================
   
   -- Create triggers
   CREATE TRIGGER on_auth_user_created
     AFTER INSERT ON auth.users
     FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
   
   -- =============================================================================
   -- ENABLE ROW LEVEL SECURITY
   -- =============================================================================
   
   ALTER TABLE public.your_main_table ENABLE ROW LEVEL SECURITY;
   
   -- =============================================================================
   -- RLS POLICIES
   -- =============================================================================
   
   CREATE POLICY "Users can view their own records"
     ON public.your_main_table
     FOR SELECT
     USING (auth.uid() = user_id);
   
   CREATE POLICY "Users can create their own records"
     ON public.your_main_table
     FOR INSERT
     WITH CHECK (auth.uid() = user_id);
   
   -- =============================================================================
   -- REALTIME CONFIGURATION
   -- =============================================================================
   
   -- Enable realtime if needed
   ALTER PUBLICATION supabase_realtime ADD TABLE public.your_main_table;
   ```

5. **Important Notes for Migration Content:**
   - **DO NOT** include RLS policies for materialized views
   - **DO NOT** use `ALTER MATERIALIZED VIEW ... ENABLE ROW LEVEL SECURITY`
   - Order matters: Extensions → Tables → Functions → Triggers → RLS → Policies
   - Use `auth.uid()` instead of `auth.user()` for better performance

### Step 9: Apply Migration to New Project

1. **Test Migration Syntax**
   ```bash
   supabase db push --dry-run
   ```

2. **Apply Migration**
   ```bash
   supabase db push
   ```
   - Type `Y` when prompted
   - Watch for any errors

3. **Handle Common Errors:**

   **Error: "file name must match pattern"**
   - Solution: Migration files must use format `YYYYMMDDHHMMSS_name.sql`

   **Error: "ALTER MATERIALIZED VIEW ... ENABLE ROW LEVEL SECURITY"**
   - Solution: Remove RLS from materialized views, comment out those lines

   **Error: "relation does not exist"**
   - Solution: Check table creation order, dependencies must be created first

4. **If Migration Fails:**
   ```bash
   # Check what went wrong
   supabase db push --debug
   
   # Fix the migration file and try again
   supabase db push
   ```

### Step 10: Deploy Edge Functions

1. **Deploy All Functions**
   ```bash
   supabase functions deploy --use-api --jobs 3
   ```
   - This uses the Management API for faster deployment
   - `--jobs 3` deploys 3 functions in parallel

2. **If Deployment Times Out:**
   ```bash
   # Deploy one function at a time
   supabase functions deploy function-name
   ```

3. **Verify Function Deployment**
   ```bash
   supabase functions list
   ```
   - Should show all your functions as ACTIVE

## Phase 4: Update Original Project

### Step 11: Get New Project Configuration

1. **Get API Keys**
   ```bash
   supabase projects api-keys --project-ref YOUR_NEW_PROJECT_REF
   ```
   - Copy the `anon` key (this is your publishable key)
   - Copy the `service_role` key (keep this secret)

2. **Note Project URL**
   - Format: `https://YOUR_NEW_PROJECT_REF.supabase.co`

### Step 12: Update Client Configuration

1. **Go to Original Project**
   ```bash
   cd /Users/yourusername/Coding/your-new-project-name
   ```

2. **Find Supabase Client File**
   ```bash
   find . -name "client.ts" -path "*/supabase/*"
   # Usually at: src/integrations/supabase/client.ts
   ```

3. **Open Client File**
   ```bash
   code src/integrations/supabase/client.ts
   # or use your preferred editor
   ```

4. **Update Configuration**
   Replace:
   ```typescript
   const SUPABASE_URL = "https://OLD_PROJECT_REF.supabase.co";
   const SUPABASE_PUBLISHABLE_KEY = "OLD_ANON_KEY";
   ```
   
   With:
   ```typescript
   const SUPABASE_URL = "https://YOUR_NEW_PROJECT_REF.supabase.co";
   const SUPABASE_PUBLISHABLE_KEY = "YOUR_NEW_ANON_KEY";
   ```

5. **Save File**

### Step 13: Update Supabase Configuration

1. **Copy New Config**
   ```bash
   cp /Users/yourusername/Coding/your-project-migration-temp/supabase/config.toml \
      supabase/config.toml
   ```

2. **Update Project ID in Config**
   ```bash
   # Open config file
   code supabase/config.toml
   ```
   
   Find line:
   ```toml
   project_id = "your-project-migration-temp"
   ```
   
   Change to:
   ```toml
   project_id = "YOUR_NEW_PROJECT_REF"
   ```

3. **Save Config File**

### Step 14: Clean Up Migrations

1. **Remove Old Migration Files**
   ```bash
   rm supabase/migrations/*.sql
   ```

2. **Copy Clean Migration**
   ```bash
   cp /Users/yourusername/Coding/your-project-migration-temp/supabase/migrations/*_initial_schema.sql \
      supabase/migrations/
   ```

3. **Verify Migration File**
   ```bash
   ls -la supabase/migrations/
   # Should show only one migration file
   ```

## Phase 5: Testing and Verification

### Step 15: Install Dependencies

1. **Check Package Manager**
   ```bash
   # Check what lock files exist
   ls -la | grep -E "(package-lock|yarn.lock|bun.lockb)"
   ```

2. **Install Dependencies**
   ```bash
   # If you have package-lock.json
   npm install
   
   # If you have yarn.lock
   yarn install
   
   # If you have bun.lockb
   bun install
   ```

3. **Verify Installation**
   ```bash
   # Check if vite is available
   npx vite --version
   ```

### Step 16: Test Local Development

1. **Start Supabase Backend**
   ```bash
   supabase start
   ```
   - This will download Docker images (first time takes several minutes)
   - Wait for "Started supabase local development setup"

2. **Verify Supabase Status**
   ```bash
   supabase status
   ```
   Expected output:
   ```
   API URL: http://127.0.0.1:54321
   DB URL: postgresql://postgres:postgres@127.0.0.1:54322/postgres
   Studio URL: http://127.0.0.1:54323
   Inbucket URL: http://127.0.0.1:54324
   ```

3. **Start Frontend Application (New Terminal)**
   ```bash
   # Open new terminal, navigate to project
   cd /Users/yourusername/Coding/your-new-project-name
   
   # Start development server
   npm run dev
   # or yarn dev, or bun run dev
   ```

4. **Access Applications**
   - **Your App**: http://localhost:5173 (or port shown in terminal)
   - **Supabase Studio**: http://127.0.0.1:54323
   - **API**: http://127.0.0.1:54321

### Step 17: Verify Functionality

1. **Test Database Connection**
   - Open Supabase Studio: http://127.0.0.1:54323
   - Navigate to "Table Editor"
   - Verify your tables are present

2. **Test Application Features**
   - Open your app in browser
   - Test user registration/login
   - Test main application features
   - Check browser console for errors

3. **Test Edge Functions**
   - In Supabase Studio, go to "Edge Functions"
   - Verify all functions are listed
   - Test a function call if possible

4. **Check RLS Policies**
   - In Supabase Studio, go to "Authentication" → "Policies"
   - Verify RLS policies are present and enabled

### Step 18: Test Remote Connection

1. **Stop Local Supabase**
   ```bash
   supabase stop
   ```

2. **Test with Remote Database**
   - Restart your frontend app
   - It should now connect to remote Supabase project
   - Test basic functionality

3. **If Remote Connection Fails:**
   - Check client.ts configuration
   - Verify API keys are correct
   - Check browser network tab for errors

## Phase 6: Final Cleanup and Commit

### Step 19: Clean Up Files

1. **Remove Temporary Directory**
   ```bash
   rm -rf /Users/yourusername/Coding/your-project-migration-temp
   ```

2. **Check for Leftover Files**
   ```bash
   cd /Users/yourusername/Coding/your-new-project-name
   ls -la | grep -E "(old_|temp_|backup_)"
   ```

3. **Remove Any Leftover Files**
   ```bash
   # Example, adjust based on what you find
   rm old_schema.sql
   rm temp_migration.sql
   ```

### Step 20: Commit Changes

1. **Check Git Status**
   ```bash
   git status
   ```

2. **Add All Changes**
   ```bash
   git add .
   ```

3. **Commit Migration**
   ```bash
   git commit -m "Complete migration to independent Supabase project

   - Migrated from Lovable-managed Supabase to independent project
   - Updated Supabase CLI to v2.33.9
   - Consolidated 50+ migration files into single clean migration
   - Updated client configuration with new project URL and API keys
   - Deployed all edge functions to new project
   - Verified local and remote functionality

   🤖 Generated with Claude Code

   Co-Authored-By: Claude <noreply@anthropic.com>"
   ```

4. **Push to Repository**
   ```bash
   git push origin main
   ```

## Phase 7: Documentation and Maintenance

### Step 21: Create Documentation

1. **Update README.md**
   ```bash
   code README.md
   ```

2. **Add Development Instructions**
   ```markdown
   ## Development Setup
   
   ### Prerequisites
   - Node.js 18+
   - Docker
   - Supabase CLI v2.33.9+
   
   ### Local Development
   1. Install dependencies:
      ```bash
      npm install
      ```
   
   2. Start Supabase backend:
      ```bash
      supabase start
      ```
   
   3. Start frontend:
      ```bash
      npm run dev
      ```
   
   ### Access Points
   - App: http://localhost:5173
   - Supabase Studio: http://127.0.0.1:54323
   - API: http://127.0.0.1:54321
   
   ### Deployment
   - Database changes: `supabase db push`
   - Edge functions: `supabase functions deploy`
   ```

3. **Save and Commit Documentation**
   ```bash
   git add README.md
   git commit -m "Add development setup documentation"
   git push origin main
   ```

### Step 22: Set Up Environment Variables (If Needed)

1. **Check for Environment Variables**
   ```bash
   grep -r "process.env" src/
   grep -r "import.meta.env" src/
   ```

2. **Create .env.local File (If Variables Found)**
   ```bash
   code .env.local
   ```

3. **Add Variables**
   ```env
   VITE_SUPABASE_URL=https://YOUR_NEW_PROJECT_REF.supabase.co
   VITE_SUPABASE_ANON_KEY=YOUR_NEW_ANON_KEY
   ```

4. **Update .gitignore**
   ```bash
   echo ".env.local" >> .gitignore
   git add .gitignore
   git commit -m "Add .env.local to gitignore"
   git push origin main
   ```

## Ongoing Maintenance

### Adding New Migrations
```bash
# Create new migration
supabase migration new feature_name

# Edit the migration file
code supabase/migrations/TIMESTAMP_feature_name.sql

# Apply to local database
supabase db push

# Test locally, then apply to remote
supabase db push --linked
```

### Adding New Edge Functions
```bash
# Create new function
supabase functions new function-name

# Edit function
code supabase/functions/function-name/index.ts

# Deploy function
supabase functions deploy function-name
```

### Monitoring and Debugging
```bash
# Check local status
supabase status

# View logs
supabase functions logs function-name

# Debug database
supabase db logs
```

## Common Issues and Solutions

### Issue: Migration Fails with "relation does not exist"
**Solution:**
1. Check table creation order in migration
2. Ensure dependencies are created first
3. Use `IF NOT EXISTS` where appropriate

### Issue: RLS Policy Errors on Materialized Views
**Error:** `"table_name" is not a table (SQLSTATE 42809)`
**Solution:** Remove RLS policies from materialized views - they don't support them

### Issue: Function Deployment Timeout
**Solutions:**
1. Use `--use-api --jobs 3` for parallel deployment
2. Deploy functions individually if still timing out
3. Check Docker memory allocation

### Issue: Port Conflicts
**Error:** `Port 54321 is already in use`
**Solutions:**
1. Stop conflicting services: `supabase stop`
2. Check what's using the port: `lsof -i :54321`
3. Kill conflicting processes if safe to do so

### Issue: Authentication Not Working
**Checklist:**
1. Verify client.ts has correct URL and keys
2. Check RLS policies are correctly configured
3. Verify auth.users table exists
4. Check browser network tab for API errors

## Success Verification Checklist

- [ ] New GitHub repository created and code pushed
- [ ] New Supabase project created independently
- [ ] Supabase CLI updated to latest version
- [ ] All migration files consolidated into single clean migration
- [ ] Migration applied successfully to new project
- [ ] All edge functions deployed successfully
- [ ] Client configuration updated with new URL and keys
- [ ] Local development environment working (`supabase start`)
- [ ] Frontend application starts and connects to database
- [ ] User authentication working
- [ ] Main application features functional
- [ ] RLS policies working correctly
- [ ] Edge functions callable and working
- [ ] Remote database connection working
- [ ] Documentation updated
- [ ] Changes committed and pushed to repository

## Benefits Achieved

✅ **Complete Independence**: No Lovable dependency
✅ **Modern Tooling**: Latest Supabase CLI with all features
✅ **Clean History**: One migration instead of 50+ fragments
✅ **Local Development**: Proper `supabase start` functionality
✅ **Full Control**: Can add migrations, functions, and deploy independently
✅ **Professional Workflow**: Standard Supabase development practices
✅ **Future-Proof**: Easy to maintain and extend

Your project is now completely independent and ready for professional development!
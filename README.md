# AI Smart City Reporter

> **AI Smart City Reporter** is a spec-driven, mobile-first civic infrastructure grievance reporting and tracking platform. Citizens upload photos of civic issues (potholes, garbage dumps, broken streetlights, water leaks); Google Gemini Vision AI classifies the issue and gauges severity; and urban municipal officers prioritize and update resolution status from an authenticated operations dashboard.

---

## Architecture & Tech Stack

- **Framework:** Next.js 15 / 16 (App Router, TypeScript)
- **Styling:** Tailwind CSS (Mobile-first responsive UI)
- **Database & Storage & Auth:** Supabase (PostgreSQL + Object Storage `complaint-images` + Supabase Auth)
- **AI Classification Engine:** Google Gemini Vision (`gemini-1.5-flash` / `gemini-2.0-flash`)
- **Validation:** Zod Schema Enforcement
- **Testing & Demo:** Playwright Browser Automation (Microsoft Edge / Chromium)
- **Deployment:** Vercel via GitHub CI/CD

---

## System Specification & Tracking

- [System Specification Document (`docs/SPEC.md`)](./docs/SPEC.md)
- [Task Implementation Checklist (`docs/TASKS.md`)](./docs/TASKS.md)

---

## End-to-End Demo Screenshots

The complete workflow was executed and verified via automated browser agent. Screenshots are captured in [`docs/demo/`](./docs/demo/):

| Step | Screen | Screenshot |
|---|---|---|
| **1** | **Citizen Home / Upload** | ![Upload Page](./docs/demo/01_citizen_upload_page.png) |
| **2** | **Capture & Geolocation** | ![Filled Form](./docs/demo/02_citizen_form_filled.png) |
| **3** | **AI Triage & Live Tracking** | ![Track Reported](./docs/demo/03_track_reported.png) |
| **4** | **Officer Portal Login** | ![Admin Login](./docs/demo/04_admin_login.png) |
| **5** | **Operations Dashboard** | ![Admin Dashboard](./docs/demo/05_admin_dashboard.png) |
| **6** | **Status Transition (In Progress)** | ![Status Updated](./docs/demo/06_admin_status_updated.png) |
| **7** | **Citizen Tracking Progression** | ![Track Updated](./docs/demo/07_track_in_progress.png) |

---

## Local Development Setup

### 1. Prerequisites
- Node.js 18+ (LTS)
- npm or pnpm

### 2. Installation
```bash
# Clone the repository
git clone https://github.com/your-username/smart-city-reporter.git
cd smart-city-reporter

# Install dependencies
npm install
```

### 3. Environment Variables Setup
Copy the environment template:
```bash
cp .env.example .env.local
```

Populate `.env.local` with your credentials:
```bash
# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# Google Gemini API Key
GEMINI_API_KEY=your-gemini-api-key
GEMINI_MODEL=gemini-1.5-flash

# Base App URL
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

> **Note on Resilient Fallback:** The application includes a self-contained local fallback database and mock storage. If Supabase or Gemini keys are not yet configured, the application functions seamlessly for demonstration and local triage without crashing.

### 4. Supabase Database Migration
In your Supabase project SQL Editor, run:
[`supabase/migrations/01_create_complaints.sql`](./supabase/migrations/01_create_complaints.sql)

This initializes:
- `complaints` table with UUID primary keys, check constraints, and auto-updating `updated_at` trigger.
- Spatial and filter performance indexes (`status`, `category`, `priority DESC`, `created_at DESC`, `lat, lng`).
- Storage bucket `complaint-images` with public read/insert policies.

### 5. Running the Application
```bash
# Run in development mode
npm run dev

# Or build and run production server
npm run build
npm run start
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## Verification & Testing

```bash
# Typecheck
npx tsc --noEmit

# Lint
npm run lint

# AI & Fallback Unit Tests
npx tsx tests/ai-test.ts

# API Services Tests
npx tsx tests/api-test.ts

# Browser Agent End-to-End Demo Flow
node tests/browser-demo.mjs
```

---

## Vercel Deployment Guide

1. Push your code to GitHub:
   ```bash
   git add .
   git commit -m "feat: complete ai smart city reporter"
   git push -u origin main
   ```

2. Open the [Vercel Dashboard](https://vercel.com/) and click **"Add New Project"**.
3. Import your `smart-city-reporter` repository.
4. Framework Preset: **Next.js**.

### Environment Variables Checklist for Vercel
In the Vercel Project Settings &rarr; **Environment Variables**, add the following keys:

| Variable Name | Description | Example |
|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase project URL | `https://xyzcompany.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Public Supabase anon key | `eyJhbGciOiJIUzI1NiIsInR5cCI6...` |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-only Supabase service role key | `eyJhbGciOiJIUzI1NiIsInR5cCI6...` |
| `GEMINI_API_KEY` | Google AI Gemini API key | `AIzaSy...` |
| `GEMINI_MODEL` | Gemini vision model | `gemini-1.5-flash` |
| `NEXT_PUBLIC_APP_URL` | Production domain | `https://smart-city-reporter.vercel.app` |

5. Click **Deploy**. Vercel will build and deploy the application automatically from `main`.

# CareerTrack AI — Student Job Application Assistant

A web-based assistant that helps college students and fresh graduates organize their job and internship applications in one place: track opportunities, deadlines, and application statuses, maintain a résumé and skills profile, and get AI-assisted skills comparison and résumé feedback.

> **Project status:** Academic project for **IT415 — Application Development and Emerging Technologies**. The system described in the proposal is under development. All AI output in this system is **draft feedback for user review** — it is never applied automatically, and it is never used to make hiring decisions.

**Repository:** https://github.com/eusarco/careertrack-ai
**Live URL (Vercel):** _(to be added after the first production deployment)_

---

## Features (MVP)

| # | Feature | Description |
|---|---------|-------------|
| F1 | Accounts & Profile | Register and log in with Supabase Auth; manage a basic profile. |
| F2 | Opportunity & Application Records | Save job/internship opportunities (employer, position, requirements, deadline, source link) and record applications against them. |
| F3 | Status Tracking & Deadlines | Track each application through Saved → Applied → Assessment → Interview → Offer / Rejected / Withdrawn, with deadline monitoring and in-system reminders. |
| F4 | Résumé & Skills Profile | Maintain a résumé summary, skills list, and experience notes. |
| F5 | AI Skills Comparison | Compare your skills with a job description to surface relevant skills and possible skill gaps (draft, user-reviewed). |
| F6 | AI Résumé Feedback & Progress Summaries | Get AI-assisted résumé improvement suggestions and a short summary of your application progress (draft, user-reviewed). |

**Explicitly out of scope:** automatic job applications or submissions, scraping or direct integration with job platforms, employer-side screening or hiring decisions, payments, and native mobile apps.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Web application | Next.js (pages + server-side API routes) |
| Database & Auth | Supabase (PostgreSQL, Auth, Row Level Security) |
| AI | A Generative AI API, called **only from server-side routes** |
| Version control | Git + GitHub |
| Hosting / CI previews | Vercel (Preview Deployment per pull request; Production on merge to `main`) |

## Getting Started

### Prerequisites

- Node.js 18 or later and npm
- A Supabase account (free tier is enough)
- An API key for the selected Generative AI provider (kept server-side only)

### 1. Clone and install

```bash
git clone https://github.com/eusarco/careertrack-ai.git
cd careertrack-ai
npm install
```

### 2. Configure environment variables

Copy the example file and fill in your own values. **Never commit a real `.env.local` file or any API key to this repository.**

```bash
cp .env.example .env.local
```

`.env.example` lists variable **names only**:

```env
# Supabase (Project Settings → API)
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=
SUPABASE_SERVICE_ROLE_KEY=        # server-only — never expose to the browser

# Generative AI provider (server-only)
AI_API_KEY=                       # stored in Vercel env vars in production
AI_MODEL=
```

Variables prefixed with `NEXT_PUBLIC_` are visible in the browser. Anything secret (service-role key, AI API key) must **not** use that prefix and must only be read by server-side API routes.

### 3. Set up the database (Supabase)

1. Create a new Supabase project.
2. In the SQL Editor, run the schema in `supabase/schema.sql` (creates the tables below and enables Row Level Security).
3. Confirm in **Authentication → Policies** that every table has RLS enabled with the rule: users can access only their own rows (`owner_id = auth.uid()`; for `profiles`, `id = auth.uid()`).

**Tables:** `profiles`, `job_opportunities`, `applications`, `resume_profiles`, `ai_feedback`.

### 4. Run the app

```bash
npm run dev        # http://localhost:3000
npm run build      # production build
npm run start      # run the production build locally
npm run lint       # lint checks
```

## Project Structure

```
careertrack-ai/
├─ app/                  # Next.js pages (landing, register, login, dashboard,
│                        # applications, resume, admin) and layout
│  └─ api/ai/            # Server-side AI routes (API key stays on the server)
│     ├─ skills-compare/ # POST /api/ai/skills-compare
│     ├─ resume-feedback/# POST /api/ai/resume-feedback
│     └─ progress-summary/ # POST /api/ai/progress-summary
├─ components/           # Reusable UI components
├─ lib/                  # Supabase client, helpers, validation
├─ supabase/
│  └─ schema.sql         # Tables + Row Level Security policies
├─ public/               # Static assets
├─ .env.example          # Variable NAMES only — no real secrets
├─ .gitignore            # Excludes .env*.local, node_modules, build output
└─ README.md
```

## How We Work (Git & GitHub Workflow)

- **`main`** — production only. Merging to `main` triggers the Vercel Production Deployment.
- **`dev`** — integration branch where finished features land first.
- **`feature/<short-name>`** — one branch per task, created from `dev` (e.g., `feature/application-form`); `fix/<short-name>` for bug fixes.
- Every change goes through a **Pull Request into `dev`**: Vercel builds a **Preview Deployment** automatically, a teammate reviews and tests on the preview link, and only approved PRs are merged. No one merges their own PR without a review.
- Releases: open a PR from `dev` → `main`, review, merge, then verify the production URL, sign-in, and one AI request.

## Privacy & AI Safety

- Personal data is protected under the **Data Privacy Act of 2012 (RA 10173)**. The system collects only what the features need, and Row Level Security ensures users can access only their own records.
- AI requests send the **minimum data needed** (e.g., your skills and the target job description) and are made only from the server.
- AI responses are stored as drafts in `ai_feedback` with the user's action (Saved / Edited / Discarded / Reported). The user can always proceed without using AI output, and the app keeps working — with a clear fallback message — if the AI service is unavailable.

## Team (IT415 Project Group — 3 Members)

| Member | Role |
|--------|------|
| Edmund U. Sarco | Project Lead / Analyst & Documentation Lead |
| John Rey M. Balitor | Full-Stack Developer |
| Carmila J. Rosales | UI/UX Designer / Tester |

# Step-by-Step Implementation Plan for Pinnacle Punch & Drill

This guide outlines a phased approach, from initial setup through deployment and monitoring. Each phase includes security best practices (authentication, data protection, error handling).  

---

## Phase 1: Project Setup & Architecture

1. Initialize Monorepo & Tooling
   • Scaffold Next.js 14 + TypeScript app via Bolt.  
   • Install Tailwind CSS, Shadcn UI, ESLint, Prettier, and Husky for git hooks.  
   • Configure `next.config.js` for strict headers and CSP.  
   • Add `vercel.json` with environment variables placeholders (no secrets in code).

2. Define Component Boundaries & Folder Structure  
   • `/app` for pages and layouts (Next.js App Router).  
   • `/components`, `/lib` (utilities & API clients), `/hooks`, `/services` (Supabase, Clerk).  
   • `/db` for SQL migrations (using Supabase CLI).  

3. CI/CD Pipeline Setup  
   • Configure GitHub Actions or Vercel Integrations: lint → test → deploy.  
   • Integrate SCA tools (e.g., Dependabot) to scan deps for vulnerabilities.  
   • Enforce merge protections and code reviews.

---

## Phase 2: Database & Supabase Configuration

1. Design Schema & Row-Level Security (RLS)
   • Tables: `companies`, `users`, `roles`, `job_sites`, `geofences`, `time_entries`, `adjustments`, `notifications`.  
   • Define RLS policies: users can `SELECT`/`INSERT` only on their `company_id`; foreman sees crew, admins see all.  

2. Configure Supabase
   • Create project, enable RLS by default.  
   • Create service role key in Vault (do not embed in code).  
   • Write SQL migrations for initial schema and policies; deploy via Supabase CLI.

3. Data Encryption & Backups  
   • Ensure nightly backups via Supabase automated backups.  
   • Enforce TLS 1.2+ for all DB connections.  
   • Limit DB user privileges: only allow necessary CRUD ops.

---

## Phase 3: Authentication & Authorization

1. Integrate Clerk for Authentication
   • Configure Clerk in Next.js, enforce `middleware.ts` to protect routes.  
   • Enable “Stay logged in for 30 days” with secure, HttpOnly cookies.  
   • Enforce password policy (min 12 chars, complexity) in Clerk dashboard.  

2. JWT & Session Security
   • Validate JWTs on Supabase Edge Functions.  
   • Check `exp` claim and `aud`/`iss`.  
   • Rotate signing keys periodically (store in Vault).  

3. Role-Based Access Control
   • Define roles in Clerk metadata and mirror them in Supabase `roles` table.  
   • On server/API layer, verify role before executing sensitive operations.  
   • Least privilege: employees can only clock themselves, foremen can approve crew, etc.

4. Multi-Factor Authentication (MFA)
   • Optionally enable SMS/Email-based MFA for Admin/Pay-Manager accounts.

---

## Phase 4: Geofencing & Clock-In/Out Logic

1. Job Site & Geofence Management
   • UI: CRUD job sites with circle/polygon drawing (e.g., Mapbox GL + React).  
   • Persist `geofences` in Postgres with GeoJSON.  

2. Device GPS Handling
   • In Next.js app, request browser `navigator.geolocation` with fallback.  
   • Validate coordinates on client; re-validate on server to prevent spoofing.  

3. Geofence Check Algorithm
   • Implement Point-in-Polygon (or circle) check in Edge Function.  
   • Return `200 OK` with allowed clock-in, else error message.

4. Clocking Endpoints in Edge Functions
   • `POST /api/clock`: validate user, geofence, idempotency token, timestamp.  
   • Write to `time_entries` with `status: pending` if offline sync.  
   • Secure: parameterized queries, strict CORS (allow only app origin).

---

## Phase 5: Offline Sync & Conflict Resolution

1. Client-Side Storage
   • Use IndexedDB (via a pouch-like library) to store pending events.  
   • Encrypt local data at rest (e.g., IndexedDB encryption plugin).

2. Synchronization Worker
   • Background sync when connection restores.  
   • Batch POSTs to `/api/sync` with exponential backoff.  

3. Conflict Handling
   • On server: detect duplicate or out-of-order timestamps.  
   • Return conflict code `409` with server’s canonical entry.  
   • UI: prompt user to retry or accept server data.

4. Security & Integrity
   • Sign offline payloads with user’s JWT to prevent tampering.

---

## Phase 6: Notifications & Messaging

1. Notification Queue & Rate Limiting
   • Implement a Redis-backed queue (or Supabase real-time + pg_cron).  
   • Use Twilio/SendGrid secrets from Vault.  

2. Edge Functions for Events
   • `onLateClockIn`, `onMissingPunch`, `onGeofenceExit`, `weeklySummary`.  
   • Validate payloads, idempotency, retry with backoff.

3. SMS & Email Templates
   • Use templating library (e.g., Handlebars) with strict escaping.  
   • Sanitize user data to prevent injection.

4. Monitoring & Alerts
   • Log failures to a secure logging service (e.g., Sentry with PII stripping).  
   • Alert on queue backlog or repeated errors.

---

## Phase 7: Frontend UI Implementation

1. Layouts & Design System
   • Implement global layout with color scheme (#E85801, #222222, #F7F7F7).  
   • Define Tailwind config and typography plugin for Rubik 700 & Open Sans 500.

2. Dashboard Components
   • Home: weekly summary cards, real-time updates (via Supabase real-time).  
   • Admin/Pay-Manager: grid/table of time entries, filters, manual adjustment modal.  
   • Foreman: crew list, approval buttons with audit trail.

3. Accessibility & Responsive Design
   • Large tap targets, ARIA roles, keyboard nav.  
   • Test on mobile & tablet breakpoints.

4. Security on UI
   • Escape all dynamic data (prevent XSS).  
   • Set CSP meta tags, SRI for any external scripts.  
   • Use anti-CSRF tokens for mutation routes.

---

## Phase 8: Payroll Rules & Compliance

1. Time Calculations
   • Server-side: auto-deduct 45-minute lunch after 7 hours.  
   • Configure overtime rules per state in a `compliance_rules` table.

2. Manual Adjustments & Audit Trail
   • When saving an adjustment, insert into `adjustments` with user, timestamp, reason.  
   • Enforce RLS so only admins or pay-managers can insert.

3. Data Exports & Reports
   • CSV/Excel: stream response using parameterized queries.  
   • PDF: generate server-side (e.g., Puppeteer) with sanitized templates.

---

## Phase 9: Third-Party Integrations

1. QuickBooks Desktop (Phase 1)
   • Build export to IIF or CSV format supported by QuickBooks.  
   • Secure file delivery via presigned URLs (expires in minutes).

2. Future Payroll APIs (Phase 2+)
   • Abstract integration layer `/services/payroll`.  
   • Plug in ADP or Gusto SDKs, with OAuth2 flows and token storage in Vault.

3. AI Anomaly Detection (Optional)
   • Edge Function to call OpenAI with time-gap data; validate and log responses.  
   • Rate-limit calls and mask prompts to avoid exposing PII.

---

## Phase 10: Testing, Security Review & Deployment

1. Automated Testing
   • Unit tests: Jest + React Testing Library.  
   • Integration tests: Supabase local emulator, Playwright for E2E.

2. Security Testing
   • Static analysis: ESLint security plugins, Snyk.  
   • Dynamic analysis: OWASP ZAP scan against staging.  
   • Penetration test critical flows (auth, sync, geofence).

3. Deployment & Monitoring
   • Deploy to Vercel with environment secrets in Vercel Vault.  
   • Set up real-time logs, metrics (via Datadog or New Relic).  
   • Configure alerts for error rates, latency, queue backlogs.

4. Post-Launch Hardening
   • Disable debug routes, verbose errors.  
   • Rotate credentials.  
   • Review RLS policies and CSP.

---

**By following this phased roadmap—with security, scalability, and compliance baked in—you’ll deliver a robust, maintainable, and production-ready Pinnacle Punch & Drill application.**
# Pinnacle Punch & Drill – Project Requirements Document

## 1. Project Overview

Pinnacle Punch & Drill is a web-based time-tracking platform built specifically for construction companies. It solves the common problem of inaccurate or missing time records on the job site by giving field employees a simple, geofenced clock-in/out interface that works even offline. Foremen can review and approve crew hours, and payroll staff get clean, exportable data to streamline pay runs.

We’re building this tool to reduce payroll errors, enforce compliance with break and overtime rules, and cut down on administrative overhead. Success means:

*   99% accurate time entries (no missed punches),
*   80% reduction in manual time corrections,
*   Payroll run time cut by half compared to current processes.

## 2. In-Scope vs. Out-of-Scope

### In-Scope

*   Secure role-based accounts for Admin, Pay Manager, Foreman, Employee
*   Geofenced clock-in/out (circle & polygon) with offline caching + auto-sync
*   Automatic 45-minute lunch deduction after 7 hours, configurable overtime multipliers
*   Real-time weekly-hours summary on Home Dashboard
*   Foreman dashboard for crew review & approval
*   Admin/Pay-Manager dashboard for alerts, manual adjustments with full audit trail
*   Job-site geofence management (add/edit multiple sites, shapes, radius)
*   Push notifications (SMS/email) for late punches, missing punches, approval requests, weekly summaries, geofence exits
*   Time export: CSV, Excel, printable PDF
*   QuickBooks Desktop integration (Phase 1)
*   Time-zone auto-detection based on device
*   “Stay logged in for 30 days” option
*   Optional AI features: anomaly detection (gaps >2 hrs), smart clock-in prompts

### Out-of-Scope (Phase 1)

*   Native iOS/Android apps (web app optimized for mobile browsers only)
*   ADP or other payroll API integrations (beyond QuickBooks Desktop)
*   Additional roles (HR reviewer, auditor, project manager)
*   Advanced AI features beyond anomaly flags and prompts
*   Multi-language support (English only)
*   Offline PDF generation

## 3. User Flow

When a new user arrives, they hit a Clerk-powered login/signup form. After entering their credentials, Clerk issues a JWT and routes them to the Home Dashboard based on role. Employees see a big clock-in/out button, their current week’s hours, and any pending notifications. Foremen and Admins land on role-specific dashboards loaded with crew summaries, alerts, and management tools.

On the job, an employee taps “Clock In.” The app checks GPS against saved geofences (circles or polygons). If offline, the punch is stored locally and synced when online. The system auto-deducts lunch after seven hours. Leaving the site prompts an SMS reminder to clock out. Foremen later log in, review entries by day/week, add notes or approve, and trigger email alerts. Admins and Pay Managers handle any final manual tweaks, export data, and push to QuickBooks.

## 4. Core Features

*   **Role-Based Access Control**: Four roles (Admin, Pay Manager, Foreman, Employee) with distinct UI and permissions.

*   **Geofenced Clock-In/Out**: Supports circle & polygon shapes, multiple sites per company, offline punches with local cache.

*   **Automated Break & Overtime Rules**: 45-min lunch after 7 hrs, configurable overtime multipliers per employee/site or company-wide.

*   **Real-Time Summaries**: Live weekly totals, plus daily/monthly breakdowns.

*   **Foreman Approval Workflow**: Crew time entries review, annotation, and approval before payroll.

*   **Admin/Payroll Manager Tools**: Late-punch alerts, missing-punch dashboards, manual adjustment with user/time/reason audit trail.

*   **Notifications Engine**:

    *   Late Clock-In (SMS to employee; email to Foreman & Admin)
    *   Missing Punch (SMS to employee; email to Foreman & Admin)
    *   Approval Request (email to Foreman & Admin)
    *   Geofence Exit Reminder (SMS to employee)
    *   Weekly Summary (email to all roles)

*   **Export & Integration**: CSV, Excel, PDF exports; QuickBooks Desktop sync.

*   **Time-Zone Auto-Detection**: Uses device settings to normalize entries.

*   **“Stay Logged In”**: 30-day persistent session.

*   **AI Assistance (Optional)**: OpenAI-powered anomaly detection (flags >2 hr gaps), smart prompts on geofence entry, suggested notes.

## 5. Tech Stack & Tools

**Frontend**

*   Next.js 14 with React & TypeScript
*   Tailwind CSS for utility-first styling
*   Shadcn UI for prebuilt accessible components

**Backend & Storage**

*   Supabase (PostgreSQL + Row-Level Security)
*   Supabase Edge Functions for serverless logic

**Authentication**

*   Clerk (OAuth, email/password) issuing JSON Web Tokens (JWT)

**Notifications**

*   Supabase Edge Functions + Twilio for SMS
*   Supabase Edge Functions + SendGrid for email

**Deployment & Dev Tools**

*   Vercel for CI/CD & hosting
*   Bolt for project scaffolding & best-practice boilerplate
*   Cursor IDE integration for AI-powered coding assistance

**AI Integration**

*   OpenAI API for prompt generation and anomaly detection

## 6. Non-Functional Requirements

*   **Performance**:

    *   Initial page load <2 seconds
    *   Dashboard data updates <500 ms

*   **Security**:

    *   All traffic over HTTPS/TLS
    *   RLS policies enforcing row-level access in Postgres
    *   OWASP Top 10 compliance

*   **Reliability**:

    *   Offline support with reliable sync once online
    *   99.9% uptime SLA for core features

*   **Usability**:

    *   Card-based, minimal design with large tap targets for tablets/phones
    *   WCAG 2.1 AA accessible components

*   **Compliance**:

    *   Configurable rules for state-specific overtime and holiday pay
    *   Detailed audit trail for all manual changes

## 7. Constraints & Assumptions

*   Relies on Supabase, Clerk, Twilio, SendGrid, OpenAI availability and quotas.
*   GPS accuracy varies by device; expect minor boundary drift.
*   No native mobile binaries—must run in modern mobile browsers.
*   Four core roles only; extra roles on backlog.
*   QuickBooks Desktop uses file-based import; live cloud integrations (e.g., ADP) are future enhancements.
*   User devices have local storage capacity for offline caches.

## 8. Known Issues & Potential Pitfalls

*   **Sync Conflicts**: Two offline punches close in time can collide—implement last-write-wins or merge UI.
*   **Geofence Drift**: GPS jitter may trigger false exit reminders—add a 30-second grace period before SMS.
*   **Rate Limits & Costs**: Twilio SMS volume spikes could hit daily limits—batch non-urgent notifications or offer email fallback.
*   **Anomaly False Positives**: Gaps >2 hrs may be normal breaks—allow configurable thresholds per company.
*   **Polygon Complexity**: Very large or complex polygons may slow boundary checks—limit vertex count or simplify shapes server-side.

This document captures all requirements for the Pinnacle Punch & Drill MVP. It is sufficiently detailed to guide AI-driven generation of technical designs, frontend guidelines, backend structures, and implementation plans without any missing pieces.

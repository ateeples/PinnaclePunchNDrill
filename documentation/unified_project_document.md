# Unified Project Documentation

## Project Requirements Document

### 1. Project Overview

Pinnacle Punch & Drill is a web-based time-tracking platform tailored for construction companies. It addresses the challenge of inaccurate or missing time records by offering geofenced clock-in at multiple authorized locations, passive GPS tracking throughout the day, and automated compliance rules. The platform aims to help field employees, foremen, and payroll staff record hours accurately, reduce errors, and speed up payroll processing.

The key objectives are to ensure every punch is recorded accurately, provide real-time summaries of hours worked, and give administrators and pay managers tools for manual adjustments with a clear audit trail. Success is measured by fewer time disputes, faster payroll runs, and high adoption by field crews and office teams.

### 2. In-Scope vs. Out-of-Scope

**In-Scope**

*   Secure role-based accounts (Admin, Pay Manager, Foreman, Employee)
*   Geofenced clock-in at multiple authorized locations
*   Passive GPS location tracking throughout the day
*   Pay managers manually assign tracked time to job codes/projects
*   Location timeline visualization for GPS trail
*   Multiple reminder systems for clock-out
*   Drive time deduction feature with manual override
*   "Still clocked in" alerts and automatic clock-out options
*   Real-time weekly and monthly hours summaries
*   Admin and Pay Manager dashboards with late clock-in alerts
*   Foreman review and approval workflow for crew hours
*   Manual time adjustments with detailed audit trails (who, when, reason)
*   Automatic 45-minute lunch deduction after 7 hours worked with no-lunch option
*   Optional notes on every clock punch
*   “Stay logged in for 30 days” feature
*   CSV, Excel, and printable PDF export of timesheets
*   QuickBooks Desktop integration (Phase 1)
*   Basic configuration of pay rates per employee, per site, or default
*   Configurable overtime multipliers and holiday/weekend rates
*   Email notifications via SendGrid for key events
*   In-app toast notifications for immediate alerts
*   Settings page for managing pay rules and notifications

**Out-of-Scope**

*   QuickBooks Online or ADP API integration (Phase 2)
*   Mobile-only native apps (iOS/Android) beyond responsive web
*   Built-in payroll calculation or tax filings
*   Advanced analytics dashboards or billing modules
*   Internationalization beyond device time-zone handling
*   Custom role types beyond the four core roles (deferred)

### 3. User Flow

A new user lands on the Pinnacle Punch & Drill homepage and signs up by entering their work email and creating a password. Clerk handles authentication, so users can reset forgotten passwords or stay logged in for up to 30 days. Once they verify their email, they pick their role or are assigned one by an admin, which determines their initial dashboard view.

After signing in, employees see a big clock-in button on their Home Dashboard, which only works at authorized locations. Throughout the day, their GPS location is passively tracked, and they can clock out from anywhere, with automatic drive time deduction if clocking out away from work locations. Foremen log in to review pending crew punches and approve or reject entries, while Admins and Pay Managers dig into dashboards showing late punches, missing entries, and weekly summary reports. All users can visit the Settings page to update personal info, adjust notification preferences, or manage pay rate rules.

### 4. Core Features

*   **Role-Based Access Control**: Four distinct roles with customized views and permissions.
*   **Geofenced Clock-In**: At multiple authorized locations.
*   **Passive GPS Tracking**: Continuous location tracking without job site geofences.
*   **Drive Time Deduction**: Automatic calculation and manual override options.
*   **Audit Trail**: Full history of manual adjustments with user, timestamp, and reason.
*   **Foreman Approval**: Crew punch review workflow before payroll processing.
*   **Admin Dashboard**: Late punch alerts, missing entry notifications, and manual adjustment tools.
*   **Real-Time Summaries**: Weekly and monthly hours views for individuals and teams.
*   **Notifications**: Email and in-app alerts for late clock-ins, missing punches, approvals, and weekly summaries.
*   **Export & Integration**: CSV, Excel, PDF exports and QuickBooks Desktop sync.
*   **Compliance Rules**: Configurable overtime, holiday/weekend rates, and lunch breaks.
*   **AI Features (Optional)**: Smart clock-in reminders, anomaly flags for gaps over two hours, suggested notes.

### 5. Tech Stack & Tools

*   **Frontend**: Next.js 14 with TypeScript, Tailwind CSS, Shadcn UI
*   **Backend & Storage**: Supabase (PostgreSQL with Row-Level Security, Edge Functions)
*   **Authentication**: Clerk with JWT tokens
*   **Notifications**: Supabase Edge Functions + SendGrid for email
*   **Mapping**: OpenStreetMap + Leaflet.js for location visualization
*   **Deployment**: Vercel hosting and serverless functions
*   **AI Integration**: OpenAI for prompts and anomaly detection
*   **Developer Tools**: Bolt for project scaffolding, Cursor for AI-assisted coding

### 6. Non-Functional Requirements

*   Response time for key API calls must be under 300ms.
*   Offline data sync should complete within 5 seconds of reconnecting.
*   All data in transit and at rest must be encrypted (HTTPS, TLS, AES-256).
*   Authentication tokens should expire after 30 days of inactivity.
*   Audit logs must be retained for at least one year.
*   The platform should support up to 1,000 concurrent users per company without performance degradation.

### 7. Constraints & Assumptions

*   Relies on device GPS for location tracking; assumes users have location services enabled.
*   Requires periodic internet to sync offline punches.
*   Uses Supabase and Clerk as core services—no alternative auth or DB providers in v1.
*   Assumes modern browser support (Chrome, Safari, Edge) on desktop and tablet.
*   Time zones detected from device; no manual override in v1.

### 8. Known Issues & Potential Pitfalls

*   **GPS Accuracy**: Location tracking may misfire in low-signal areas. Mitigation: allow adjustable radius and fallback reminders.
*   **Sync Conflicts**: Two offline punches at once could conflict. Mitigation: merge strategy with timestamps and user prompts.
*   **Notification Delays**: Email services may experience delays. Mitigation: provide in-app alerts as backup.
*   **Data Privacy**: Storing location data raises privacy concerns. Mitigation: clear consent flow and data retention policies.
*   **Complex Compliance**: State-by-state labor rules vary widely. Mitigation: flexible config and future library of rule templates.

## App Flow Document

### Onboarding and Sign-In/Sign-Up

When a new user visits Pinnacle Punch & Drill, they land on a welcome page that explains the platform’s benefits. They click Sign Up and enter their work email and a password. Clerk handles the email verification step, sending a link to confirm their address. After verification, they choose their role if permitted or wait for an admin to assign one. Existing users sign in with email and password, and they can recover a lost password through the standard reset link. On sign-in, they can check “Stay logged in for 30 days.” Signing out is a single click in the top navigation menu.

### Main Dashboard or Home Page

Once logged in, the Home Dashboard greets users by name and shows a large clock-in button at the center, which only works at authorized locations. Beneath it is a real-time summary of hours worked this week. A sidebar lists menu items for Reports, Settings, and Role-specific pages like Crew Approval or Admin Tools. A header shows notification icons and a quick access menu for account actions. From here, users can navigate to detailed reports, settings, or role-specific dashboards with a single click.

### Detailed Feature Flows and Page Transitions

Employees tap the clock-in button when they arrive at an authorized location. The system records the punch and passively tracks their GPS location throughout the day. They can clock out from anywhere, and if they clock out away from work locations, the system automatically deducts drive time. Foremen go to the Crew Approval page, review each crew member’s punches by day or week, add comments, and approve or reject entries. Admins and Pay Managers visit the Admin Dashboard, view late or missing punches, make manual adjustments in a modal that captures who made the change and why, and then publish hours for export or integration.

### Settings and Account Management

In Settings, users update personal information and notification preferences. Admins see additional tabs for Pay Rules, where they configure overtime multipliers, lunch policies, and pay rates per employee or job site. After saving changes, they return to the main dashboards via the sidebar menu or header links.

### Error States and Alternate Paths

If a user tries to clock in outside an authorized location, the app shows a clear error banner explaining the issue and gives them the option to retry. During network outages, attempts to clock in queue locally and show a small offline indicator; once the connection is back, the app syncs entries automatically and notifies users of success or any merge conflicts. If a manual adjustment fails or the notification service is down, the system logs the error and displays an in-app alert so the user can retry.

### Conclusion and Overall App Journey

From signing up to clocking in at authorized locations, reviewing crew hours, and exporting payroll data, the journey is smooth and role-driven. Employees spend seconds punching in, foremen quickly approve crew entries, and admins have all the tools they need for compliance, adjustments, and exports. The flow keeps everyone on task and minimizes errors in the payroll pipeline.

## Tech Stack Document

### Frontend Technologies

*   Next.js 14: Provides server-side rendering and fast page loads.
*   React & TypeScript: Ensures type safety and reusable components.
*   Tailwind CSS: Allows quick styling with utility classes and a consistent design system.
*   Shadcn UI: Offers prebuilt, accessible components for a polished look.

### Backend Technologies

*   Supabase (PostgreSQL): Manages data with Row-Level Security to enforce role-based rules.
*   Supabase Edge Functions: Runs serverless logic for notifications, location tracking, and AI calls.
*   Clerk Authentication: Handles secure signup/signin and JWT token management.

### Infrastructure and Deployment

*   Vercel: Hosts the frontend and edge functions for global speed.
*   GitHub + CI/CD (GitHub Actions): Automates testing and deployment on every push.
*   Git for Version Control: Tracks code changes and supports branching strategies.

### Third-Party Integrations

*   SendGrid: Delivers email notifications for late clock-ins, approvals, and summaries.
*   OpenAI: Powers optional AI features like anomaly detection and smart prompts.
*   OpenStreetMap + Leaflet.js: Provides free mapping and location visualization.
*   Bolt & Cursor: Provide AI-powered tooling for project setup and coding assistance.

### Security and Performance Considerations

*   HTTPS/TLS everywhere to protect data in transit.
*   AES-256 encryption for sensitive fields at rest.
*   Row-Level Security in Supabase for strict data access.
*   JWT tokens with 30-day expiry and refresh logic.
*   CDN and edge caching for fast asset delivery.
*   Lazy-loading and code-splitting in Next.js to optimize bundle size.

### Conclusion and Overall Tech Stack Summary

The chosen stack balances speed, security, and developer productivity. Next.js and Supabase work seamlessly for rapid feature delivery, while Clerk and third-party services handle specialized functions like messaging and AI. This setup ensures a scalable, maintainable platform that meets the needs of construction field and office teams alike.

## Frontend Guideline Document

### Frontend Architecture

We use Next.js 14 with the App Router to build pages with a mix of server-side rendering (SSR) and static generation (SSG) for top performance. React components are written in TypeScript for type safety. Tailwind CSS handles styling, and Shadcn UI provides accessible building blocks. The result is a modular, scalable codebase where each feature lives in its own folder under `/app` or `/components`.

### Design Principles

We follow usability by keeping interfaces simple and clear. Accessibility is a priority: all buttons have proper labels, and the color contrast meets WCAG standards. Responsiveness ensures the app works on desktop, tablets, and phones with large tap targets for easy use on the job site.

### Styling and Theming

We use Tailwind CSS with design tokens for colors and spacing. The brand palette is safety orange (#E85801) for calls to action, charcoal (#222222) for text, and light gray (#F7F7F7) for backgrounds. Headings use Rubik 700, body text uses Open Sans 500. The style is flat and minimal, with card-based layouts and subtle shadows for depth.

### Component Structure

Components follow an atomic design approach: small atoms (buttons, inputs) live in `/components/ui`, molecules (forms, card layouts) in `/components/molecules`, and complex organisms (dashboards, approval lists) in `/components/organisms`. This hierarchy promotes reusability and easy maintenance.

### State Management

We rely on Supabase’s React hooks or React Query for data fetching and caching. Local UI state uses React Context for global settings like theme or user info. This mix keeps data fresh without prop drilling.

### Routing and Navigation

Next.js App Router handles routing by file name under `/app`. We create nested layouts for role-based pages so that only relevant menu items appear. Dynamic routes (e.g., `/foreman/[crewId]`) show specific crew details.

### Performance Optimization

We lazy-load heavy modules like maps or charts using dynamic imports. Code-splitting is automatic in Next.js, but we also trim Tailwind CSS in production builds. Images are optimized with the built-in Next.js Image component.

### Testing and Quality Assurance

Unit tests run with Jest and React Testing Library for core components. Integration and end-to-end tests use Cypress to simulate user flows like clock-in/out and approval. Every pull request must pass tests before merging.

### Conclusion and Overall Frontend Summary

Our frontend setup uses modern tools to deliver a fast, accessible experience that adapts to the demands of construction crews and office staff. The component-based architecture, clear design system, and focus on performance ensure that adding new features is straightforward and reliable.

## Implementation Plan

1.  Initialize the project repository with Bolt, set up Next.js 14, TypeScript, Tailwind CSS, and Shadcn UI.
2.  Configure GitHub Actions for linting, testing, and deployment to Vercel.
3.  Set up Supabase project, configure PostgreSQL schema, and enable Row-Level Security.
4.  Integrate Clerk for authentication and configure JWT settings.
5.  Build the Home Dashboard layout and core clock-in component with geofence checks at authorized locations.
6.  Implement passive GPS tracking and sync logic using IndexedDB or localStorage.
7.  Create the Foreman Approval page and approval workflow.
8.  Develop the Admin/Pay Manager dashboard for alerts, manual adjustments, and audit trails.
9.  Add Settings pages for pay rules and notification preferences.
10. Integrate SendGrid via Supabase Edge Functions for email alerts.
11. Build export functionality for CSV, Excel, and PDF.
12. Stub out QuickBooks Desktop integration and test data sync.
13. Add optional OpenAI calls for anomaly detection and smart prompts.
14. Apply final styling, theme colors, and typography across all pages.
15. Write unit, integration, and end-to-end tests; ensure 80%+ coverage.
16. Deploy to production on Vercel and configure environment variables.
17. Conduct user acceptance testing with a pilot construction team.
18. Monitor logs and performance metrics, iterate on feedback, and plan Phase 2 integrations.

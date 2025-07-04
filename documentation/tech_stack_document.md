# Tech Stack Document for Pinnacle Punch & Drill

This document explains, in everyday language, the technology choices behind Pinnacle Punch & Drill — a web-based time-tracking platform built for construction companies. You don’t need a technical background to understand why we picked each tool and how it helps you and your team.

## 1. Frontend Technologies
These are the tools that build the part of the app you see and interact with.

- **Next.js 14 (with React and TypeScript)**
  - A modern web framework that helps us build fast, reliable pages.
  - TypeScript adds simple error-checking so bugs are caught early, keeping the app stable.
  - React lets us create interactive parts (like the clock-in button) that update instantly as you work.

- **Tailwind CSS**
  - A styling tool that gives us ready-made design pieces (colors, spacing, fonts).
  - Speeds up design work and ensures a consistent look across all screens (phones, tablets, desktops).

- **Shadcn UI**
  - A set of pre-built, accessible user interface components (cards, buttons, forms).
  - Ensures the app is easy to use and looks polished without building every element from scratch.

How this improves your experience:
  - Quick page loads and snappy interactions.
  - A clean, consistent design in your brand colors (safety-orange, charcoal, light-gray).
  - Large, easy-to-tap buttons and clear layouts for field workers on phones or tablets.

## 2. Backend Technologies
These power the data management, security, and core logic that run behind the scenes.

- **Supabase (PostgreSQL with Row-Level Security)**
  - Our main database stores all time entries, user profiles, settings, and audit logs.
  - Row-Level Security makes sure each user only sees the data they’re allowed to — for example, an employee sees only their own hours, while a foreman sees their crew’s hours.

- **Supabase Edge Functions**
  - Tiny pieces of code that run close to users (at the network edge) to handle tasks like sending notifications or syncing data.
  - Keeps your experience fast, even when triggering SMS or email alerts.

- **Clerk (Authentication with JWT)**
  - Manages user logins, sign-ups, and “stay logged in for 30 days” securely.
  - Uses JSON Web Tokens (JWT) so each request is proven to come from a real, signed-in user.

How these work together:
  - When you clock in, your device sends a secure request (via Clerk/JWT) to Supabase.
  - Supabase checks your role and geofence rules, then records your time entry and audit details.
  - If you’re offline, the entry is saved locally and automatically synced once you’re back online.

## 3. Infrastructure and Deployment
This covers where the app lives and how we keep it running smoothly.

- **Vercel**
  - Our hosting platform for the Next.js frontend and Supabase edge functions.
  - Automatically deploys updates whenever we push new code, so you always have the latest version.
  - Built-in content delivery network (CDN) ensures pages load quickly around the world.

- **Version Control & CI/CD**
  - We use Git (hosted on GitHub) to track every change in code, allowing safe experimentation and easy rollbacks.
  - Continuous Integration and Deployment (CI/CD) pipelines run automated tests and launch updates seamlessly.

- **Developer Tools**
  - **Bolt**: Helps us scaffold new features quickly with best practices already in place.
  - **Cursor**: An AI-powered coding assistant built into our IDE for real-time code suggestions and error checks.

Benefits:
  - Fast, reliable releases with minimal downtime.
  - Easy collaboration among developers and safe handling of code changes.

## 4. Third-Party Integrations
These services extend the platform’s capabilities without reinventing the wheel.

- **Twilio (SMS)** & **SendGrid (Email)**
  - Send real-time notifications: late clock-in alerts, missing-punch reminders, weekly summaries, and approval requests.
  - Ensures timely communication to employees, foremen, and payroll managers.

- **QuickBooks Desktop Integration**
  - Export payroll data directly into QuickBooks desktop for streamlined accounting.
  - Future option to connect to other systems like ADP via API.

- **OpenAI (Optional AI Features)**
  - Smart clock-in reminders based on geofence entries.
  - Anomaly detection to flag unexpected gaps (e.g., more than 2 hours without a punch).
  - Suggested notes to help users explain adjustments.

How these help you:
  - Automate routine communications and reduce manual follow-ups.
  - Seamless data transfer to your existing payroll software.
  - Intelligent prompts that prevent errors and speed up entries.

## 5. Security and Performance Considerations
We built Pinnacle Punch & Drill to be both secure and fast.

- **Authentication & Access Control**
  - Secure sign-in via Clerk and JSON Web Tokens.
  - Role-based permissions enforced at the database level (Row-Level Security).

- **Data Protection**
  - All data is encrypted in transit (HTTPS) and at rest in the database.
  - Detailed audit logs record who made every manual change, when, and why.

- **Offline Functionality & Sync**
  - Local caching lets you clock in/out without connectivity.
  - Automatic background sync when you regain service keeps records accurate.

- **Performance Optimizations**
  - Pages are pre-built (static generation) where possible, giving you near-instant load times.
  - Edge Functions run logic close to users for minimal delay in notifications or data updates.

## 6. Conclusion and Overall Tech Stack Summary

Pinnacle Punch & Drill combines modern web frameworks (Next.js, TypeScript, React), efficient styling (Tailwind CSS, Shadcn UI), and a powerful backend (Supabase with RLS and edge functions). Secure authentication comes from Clerk/JWT, and we host everything on Vercel for fast, global performance. Developer productivity is boosted by Bolt and Cursor. Key integrations with Twilio, SendGrid, QuickBooks, and optional AI via OpenAI round out a system designed for accuracy, reliability, and ease of use.

This stack aligns perfectly with our goals:
-  Accurate, geofenced time tracking (even offline)
-  Role-based access and detailed audit trails
-  Automated notifications and payroll exports
-  Scalability for multiple job sites and future growth (additional payroll APIs, new user roles, etc.)

By choosing these technologies, we ensure that Pinnacle Punch & Drill remains reliable, secure, and ready to evolve as your construction business grows.
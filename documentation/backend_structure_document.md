# Backend Structure Document for Pinnacle Punch & Drill

## 1. Backend Architecture

**Overview**
The backend is built on a serverless, managed stack that keeps setup and maintenance to a minimum while delivering high performance and scalability:

- **Supabase** as the primary backend-as-a-service (BaaS), providing PostgreSQL, authentication helpers, storage, and real-time capabilities.  
- **Supabase Edge Functions** for custom business logic, integrations (Twilio, SendGrid, OpenAI), and scheduled jobs.  
- **Clerk** for user authentication and role-based JWT handling.  

**Design Patterns & Frameworks**

- **Event-Driven**: Clock‐in/out, notifications, anomaly detection, and exports happen via Edge Functions triggered by user actions or scheduled tasks.  
- **RESTful API**: Supabase’s auto-generated endpoints cover standard CRUD; custom logic is exposed via HTTP endpoints in Edge Functions.  
- **Row-Level Security (RLS)**: Ensures that users only see and manipulate records belonging to their company and role.  

**Scalability, Maintainability & Performance**

- **Serverless**: Supabase and Edge Functions auto-scale to handle spikes in usage.  
- **Managed Database**: PostgreSQL scales vertically, with read-replicas available for heavy reporting.  
- **Modular Functions**: Each business process (e.g., sending notifications, anomaly checks) lives in its own function, making updates isolated and low-risk.  

## 2. Database Management

**Database Technology**

- **Type**: Relational (SQL)  
- **System**: PostgreSQL (hosted by Supabase)  

**Data Structure & Access**

- **Normalized Schema**: Core entities (Users, Companies, Jobsites, TimeEntries) are separate tables, linked by UUIDs.  
- **Geospatial Data**: Geofences stored either as JSON polygons or with PostGIS for advanced queries.  
- **Row-Level Security**: Policies ensure each role only accesses their permitted rows (e.g., an Employee only sees their own time entries).  
- **Audit Logging**: All manual adjustments are recorded in an `AuditLogs` table, capturing who, when, and why.  

**Data Management Practices**

- **Backups**: Automated daily snapshots and point-in-time recovery via Supabase.  
- **Archiving**: Older time entries (e.g., >1 year) can be moved to a cheaper storage table or offloaded to a data warehouse.  
- **Retention Policies**: Configurable per-company, meeting compliance requirements.

## 3. Database Schema

Below is a high-level, human-readable overview of key tables and their fields. Primary keys are `id` (UUID) and timestamps track creation/update.

Users
- id, company_id, role (Admin, PayManager, Foreman, Employee), name, email, phone

Companies
- id, name, timezone, address

Jobsites
- id, company_id, name, address, timezone

Geofences
- id, jobsite_id, name, shape_type (circle/polygon), coordinates (JSON)

TimeEntries
- id, user_id, jobsite_id, clock_in_at, clock_out_at, synced_at, is_offline_entry

OvertimeRules
- id, company_id, rule_type (daily/weekly), threshold_hours, multiplier

PayRates
- id, company_id, user_id (nullable), jobsite_id (nullable), rate_per_hour

Approvals
- id, foreman_id, time_entry_id, status (approved/pending/rejected), reviewed_at

Notifications
- id, user_id, type (SMS/Email), trigger_event, sent_at, status

AuditLogs
- id, time_entry_id, action (edit/delete), performed_by, reason, performed_at

ExportRequests
- id, company_id, requested_by, format (CSV/Excel/PDF), status, created_at

Anomalies (optional)
- id, time_entry_id, gap_hours, detected_at, resolved (boolean)

### SQL Schema Example (PostgreSQL)
```sql
CREATE TABLE Companies (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  timezone TEXT NOT NULL,
  address TEXT
);

CREATE TABLE Users (
  id UUID PRIMARY KEY,
  company_id UUID REFERENCES Companies(id),
  role TEXT CHECK (role IN ('Admin','PayManager','Foreman','Employee')),  
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  phone TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE Jobsites (
  id UUID PRIMARY KEY,
  company_id UUID REFERENCES Companies(id),
  name TEXT NOT NULL,
  address TEXT,
  timezone TEXT
);

-- Additional tables follow a similar pattern...
```  

## 4. API Design and Endpoints

**Approach**  
- Leverage Supabase’s auto-generated REST API for standard CRUD.  
- Use Edge Functions for custom workflows, third-party integrations, and scheduled tasks.

**Key Endpoints**

| Endpoint                      | Method | Purpose                                           |
|-------------------------------|--------|---------------------------------------------------|
| /rest/v1/users                | GET    | List or filter users (RLS enforced)               |
| /rest/v1/time_entries         | POST   | Create clock-in or manual entry                   |
| /rest/v1/time_entries/:id     | PATCH  | Record clock-out or manual edits                  |
| /functions/clock-in           | POST   | Custom logic for geofence check & offline sync    |
| /functions/clock-out          | POST   | Clock-out reminders, lunch deduction              |
| /functions/send-notification  | POST   | Trigger SMS/Email via Twilio/SendGrid             |
| /functions/anomaly-detect     | POST   | Run AI check on gaps > 2 hours (optional)         |
| /functions/export-data        | POST   | Generate CSV/Excel/PDF, push to storage or QuickBooks |

Each custom function validates JWT via Clerk, enforces RLS on database calls, and returns standardized JSON responses.

## 5. Hosting Solutions

- **Supabase**: Host the PostgreSQL database, real-time streams, storage, and Edge Functions.  
- **Vercel**: Hosts any standalone backend services (if needed) and the frontend.  

**Benefits**

- **Reliability**: Managed services with built-in failover.  
- **Scalability**: Automatic scaling of Edge Functions and database.  
- **Cost-Effectiveness**: Pay-as-you-go model; minimal ops overhead.

## 6. Infrastructure Components

- **Load Balancers**: Supabase and Vercel handle traffic routing internally.  
- **Caching**:  
  - Database query caching via PostgreSQL prepared statements.  
  - Edge caching of static assets by Vercel CDN.  
- **CDN**: Vercel CDN for frontend assets, reducing latency for global users.  
- **Storage**: Supabase Storage for any generated exports and user-uploaded files.

These components work together to ensure fast response times, reliable data delivery, and global availability.

## 7. Security Measures

- **Authentication**: Clerk manages sign-up/sign-in, issues JWTs.  
- **Authorization**: PostgreSQL Row-Level Security (RLS) policies enforce role-based data access.  
- **Encryption**:  
  - TLS for data in transit.  
  - AES-256 at rest (handled by Supabase).  
- **API Keys**: Stored securely in Supabase and Vercel environment variables (Twilio, SendGrid, OpenAI).  
- **Audit Trail**: Every manual time adjustment and approval is logged for compliance.  
- **Compliance**: GDPR-ready data controls, configurable data retention policies.

## 8. Monitoring and Maintenance

- **Logging**:  
  - Supabase logs for database queries and Edge Function invocations.  
  - Vercel function logs for any standalone services.  
  - Twilio & SendGrid dashboards for notification delivery reports.  
- **Alerts**: Set up email/SMS alerts for function errors, high database CPU, or storage thresholds.  
- **Performance Metrics**:  
  - Supabase’s built-in analytics.  
  - (Optional) Integrate Sentry or Datadog for deeper traceability.  
- **Maintenance Strategy**:  
  - Scheduled downtime for major schema changes (if ever).  
  - Regular package updates and security patches via CI/CD.  
  - Quarterly architecture review to adopt new Supabase features.

## 9. Conclusion and Overall Backend Summary

The backend for Pinnacle Punch & Drill combines a managed PostgreSQL database, serverless Edge Functions, and robust authentication to deliver a secure, scalable time-tracking solution. Key strengths include:

- **Minimal Ops**: Supabase and Vercel handle scaling, backups, and uptime.  
- **Fine-Grained Security**: RLS and JWT-based roles ensure data isolation.  
- **Extensible Design**: Modular functions make integrating new features (AI anomaly checks, QuickBooks exports) straightforward.  
- **Performance**: Global CDNs and caching keep both frontend and backend snappy for users wherever they work.

This architecture meets the project’s goals of accurate, compliant time tracking, easy payroll integration, and a future-ready platform that can grow with the needs of construction teams.

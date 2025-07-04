# Pinnacle Punch & Drill App Flow

## Onboarding and Sign-In/Sign-Up

When a new user arrives at the Pinnacle Punch & Drill site, they land on a public facing landing page that explains the time tracking features and safety benefits. From here, the user can click a prominent “Get Started” button that leads to the Clerk-powered sign-up page. On this page, they can choose to sign up with their company email address or with a supported social login, such as Google or Microsoft. When signing up by email, the user enters their name, email, and a secure password. They must agree to the terms of use and then click “Create Account.” If they choose social login, they consent to share their basic profile details and are instantly enrolled.

Upon successful registration, the user receives a welcome email with a link to verify their address. Once they verify, they are taken to a brief onboarding screen inside the app that confirms their role as Employee, Foreman, Pay Manager, or Admin based on preconfigured settings from the administrator. This role determines which dashboards and features they see.

To sign in after account creation, the user returns to the sign-in page, enters their email and password, and can select a “Stay logged in for 30 days” checkbox. If they forget their password, they click the “Forgot Password” link, enter their email, and receive a secure reset link via email. Following that link lets them choose a new password and then sign in again. When they wish to sign out, a “Sign Out” option is available in the account dropdown in the top navigation bar.

## Main Dashboard or Home Page

After signing in, the user lands on their main dashboard, which changes slightly based on role. All dashboards share a consistent layout with a top header bar showing the app logo, user profile menu, and a notification icon. A collapsible sidebar on the left provides links to Dashboard, Time Entries, Reports, and Settings. The central area displays role-specific widgets.

An Employee sees a large clock-in/clock-out button, a real-time summary of hours worked for the current week by day, and a geofence status indicator confirming whether they are on site. Beneath that, they see any pending reminders, such as a lunch deduction notice after seven hours or a clock-out prompt if they leave the job-site boundary.

A Foreman sees a similar clock-in widget along with a crew overview table that lists each team member’s day and week totals. Pending approval items appear in a highlighted panel with links to review or reject entries. The Pay Manager and Admin dashboards replace the crew table with a company-wide summary. They also see late clock-in alerts, missing punch notices, and a manual adjustment button that opens the audit trail viewer when clicked.

From any dashboard, the user can navigate to the Time Entries page to see detailed daily and monthly views, to the Reports page for exporting data, or to Settings to manage account and company preferences.

## Detailed Feature Flows and Page Transitions

When an Employee arrives on site, they tap the clock-in button, and the app uses the device GPS to check against the active geofence. If they are within the allowed circle or polygon, the punch is recorded immediately. If they are outside the boundary, a warning modal appears explaining they must be on site to clock in. The employee can optionally add a note explaining their situation. After seven hours of work, the system automatically deducts 45 minutes for lunch and posts an info message to the dashboard. If the device loses internet, the app stores the punch locally and shows a sync pending indicator in the header. When connectivity returns, the entry automatically syncs with the server.

Clock-out works the same way, with an additional automatic SMS prompt triggered by leaving the geofence. This uses the Twilio integration via a Supabase edge function. The employee sees a reminder on their phone and a notification banner in the app.

A Foreman reviewing crew hours clicks the “Review Crew” link in the sidebar. They land on a page listing each crew member’s entries for the week. Entries flagged as missing or manually adjusted appear at the top. The foreman clicks each item to open a detail modal showing date, time, notes, and any requested reason. They can approve or reject the entry, which sends an email notification via SendGrid and updates the entry status. Once they finish, they return to the dashboard using the back link in the header.

An Admin or Pay Manager starts on their dashboard and clicks “Manual Adjustments.” They see a full audit trail with badges showing who made each change, when, and why. To adjust a time, they click an entry, edit the clock-in or clock-out time, enter a reason, and submit. This generates an email alert to the Foreman for re-approval and records the change in the audit log. To configure compliance rules, they click “Company Settings” in the sidebar and are taken to a multi-tab page where they can toggle overtime multipliers, set state-specific thresholds, and define holiday pay rates.

For exporting data, the Admin navigates to Reports, chooses a date range, and selects CSV, Excel, or Printable PDF. If QuickBooks Desktop integration is enabled, they click “Sync to QuickBooks” and the system uses a secure file drop to transfer the data. Each export shows a success message and a download link.

## Settings and Account Management

The Settings page is accessible via the sidebar and the user profile menu. On this page, users manage their personal information in the Profile tab, updating name, email, password, and phone number for SMS alerts. In the Notifications tab, they choose which SMS or email alerts to receive, such as late clock-in or missing punch warnings.

Administrators see additional tabs to manage Geofences, Pay Rates, and AI Settings. In Geofences, they open an interactive map to add, edit, or delete sites. They draw circle or polygon shapes and set radius values. In Pay Rates, they choose to apply rates per employee, per job site, or at a flat company level, and enable overtime multipliers. The AI Settings tab allows toggling anomaly detection, setting a two-hour gap threshold, and enabling suggested notes.

After saving any settings, the user clicks “Return to Dashboard” in the header to resume normal usage. Changes take effect immediately, and notifications confirm successful updates.

## Error States and Alternate Paths

If a user enters invalid credentials at sign-in, an inline error message appears explaining that the email or password is incorrect and offers a link to reset the password. During clock-in or clock-out, if GPS permission is denied or the device cannot determine location, a modal asks the user to enable location services or switch to manual mode, with a warning that manual punches must be reviewed by a Foreman.

When syncing offline punches, if a conflict arises because an entry was changed by someone else, the app shows a conflict modal listing the local and server times. The user chooses which version to keep, and Clerk logs the decision. Network failures during export display a retry button and preserve the selected options so that the user does not lose their work. All error messages use clear language and suggest a path to resolution.

## Conclusion and Overall App Journey

A typical user begins by signing up with their company email and verifying their account. They sign in and are guided to the Home Dashboard tailored to their role. Employees clock in and out using geofence validation with offline support while receiving automatic lunch deductions and reminder alerts. Foremen review crew entries, approving or rejecting any adjustments. Pay Managers and Admins manage compliance, manually adjust times with full audit trails, and export data to CSV, Excel, PDF, or QuickBooks Desktop. Throughout, users customize their account and notification preferences, configure job-site geofences, and enable optional AI-driven anomaly detection. This flow ensures that each punch, approval, and export is clear, traceable, and aligned with company and legal requirements, streamlining payroll and boosting operational accuracy for construction teams.
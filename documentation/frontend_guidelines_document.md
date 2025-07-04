# Frontend Guideline Document

This document outlines the frontend architecture, design principles, and technologies for Pinnacle Punch & Drill—a web-based time-tracking platform for construction companies. It’s written in everyday language so anyone on the team can understand the setup without needing a deep technical background.

## 1. Frontend Architecture

### Overview
- **Framework & Language**: Next.js 14 with React and TypeScript. Next.js gives us file-based routing, built-in optimizations, and easy deployment on Vercel. TypeScript adds type safety, catching errors early.
- **UI Library**: Shadcn UI (built on Radix + Tailwind CSS) for pre-styled, accessible components that we can customize.
- **Styling**: Tailwind CSS for utility-first styling.

### Scalability, Maintainability & Performance
- **Modular Structure**: We organize code into logical folders (e.g., `app/`, `components/`, `ui/`) to keep things clean as the project grows.
- **Server & Client Balance**: Use Next.js’s server-side rendering (SSR) for dynamic data (like time entries) and static site generation (SSG) or incremental static regeneration (ISR) for stable pages (like marketing or help pages).
- **Edge Functions**: Notifications and some dynamic operations run on Supabase Edge Functions for low-latency, geo-distributed logic.
- **Deployment**: Hosted on Vercel, ensuring global CDN delivery and automatic scaling.

## 2. Design Principles

### Usability
- **Intuitive Layout**: Clean, card-based design grouping related information (e.g., daily punches, geofence map) so users find what they need in one view.
- **Large Tap Targets**: Buttons and form fields sized for easy tapping on mobile devices.

### Accessibility
- **WCAG 2.1 AA** compliance: Sufficient color contrast, keyboard navigation, and ARIA labels on custom components.
- **Semantic HTML**: Headings, lists, and landmarks (`<main>`, `<nav>`) help screen readers.

### Responsiveness
- **Mobile-First**: Design starts at small screens and scales up. Breakpoints follow Tailwind’s defaults unless custom sizes are needed.
- **Fluid Layouts**: Flexbox and CSS Grid ensure content adjusts to different screen sizes.

## 3. Styling and Theming

### Styling Approach
- **Tailwind CSS**: Utility-first classes for fast styling, plus JIT mode to keep bundle size small.
- **Component Themes**: Shadcn UI’s theming mechanism lets us define global color tokens and spacing in `tailwind.config.js`.

### Theming & Consistency
- **Brand Palette**:
  - Primary (Safety Orange): `#E85801`
  - Dark (Charcoal): `#222222`
  - Light (Gray): `#F7F7F7`
  - Accent Success (Green): `#28A745`
  - Accent Error (Red): `#DC3545`
  - Neutral Shades: grays from `#F7F7F7` to `#333333`
- **Typography**:
  - Headings: Rubik 700
  - Body Text: Open Sans 500
  - System-fallback fonts: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`

### Visual Style
- **Modern Flat Design**: Minimal shadows, clean lines, card layouts.
- **Subtle Depth**: Use light shadows or borders to separate cards but avoid heavy skeuomorphism.

## 4. Component Structure

### Organization
- **`app/`**: Next.js app directory for pages, nested layouts, and APIs.
- **`components/`**: Reusable React components (e.g., `ClockInButton`, `GeofenceMap`, `ApprovalModal`).
- **`ui/`**: Shadcn UI wrappers and design-system components (e.g., `Button`, `Card`, `Input`).

### Reusability & Maintainability
- **Atomic Approach**: Small “atoms” (buttons, inputs) combine into “molecules” (forms, cards), which build “organisms” (dialogs, dashboards).
- **Props-Driven**: Components accept props for data and callbacks, avoiding hard-coded values.
- **Storybook (Optional)**: Document components in isolation for easier development and testing.

## 5. State Management

### Authentication State
- **Clerk**: Provides user session, roles (Admin, Pay Manager, Foreman, Employee) via React Context under the hood.

### Data Fetching & Caching
- **Supabase Client**: Queries to Postgres run through Supabase JS client in React hooks.
- **React Query or SWR**: (Team choice) for caching, background refetching, and cache invalidation of time entries, reports, and settings.

### Local & Offline State
- **Local Caching**: IndexedDB or `localStorage` to store pending punches when offline.
- **Sync Logic**: On reconnect, queued actions auto-sync via background tasks (service worker or a hook in root layout).

## 6. Routing and Navigation

### File-Based Routing
- **Next.js App Router**: Folders under `app/` map to routes. Dynamic segments handle IDs (e.g., `/geofences/[id]`).
- **Nested Layouts**: Shared UI (header, sidebar) defined in a root `layout.tsx`.

### Protected Routes & Role Guarding
- **Middleware**: Next.js middleware intercepts requests, checks JWT via Clerk, and redirects unauthorized users to login or “Not Authorized” pages.
- **Client-Side Guards**: Components verify roles before rendering sensitive UI (e.g., manual adjustment panels).

### Navigation UI
- **Sidebar + Top Nav**: Collapsible sidebar for sections (Dashboard, Timecards, Reports, Settings). Top nav shows user menu and notifications.
- **Breadcrumbs**: Show current location in nested workflows (e.g., `Settings / Geofences / New`).

## 7. Performance Optimization

### Code Splitting & Lazy Loading
- **Dynamic Imports**: Load heavy components (e.g., map view) only when needed.
- **React.lazy & Suspense**: Wrap dynamic parts to show a spinner or skeleton.

### Asset Optimization
- **Tailwind PurgeCSS**: Removes unused CSS classes in production.
- **Next/Image**: Automatic resizing, compression, and lazy loading for images.

### Network & Caching
- **Edge Caching**: Vercel CDN and Supabase Edge Functions cache API responses near users.
- **HTTP Caching Headers**: Set on static assets and API routes for longer TTLs where possible.

## 8. Testing and Quality Assurance

### Unit & Component Tests
- **Jest + React Testing Library**: Test component rendering, user interactions, and utility functions.

### End-to-End Tests
- **Cypress** or **Playwright**: Simulate real user flows (clock-in, geofence creation, report export).

### Linting & Formatting
- **ESLint** with TypeScript rules, plus plugin for Tailwind class ordering.
- **Prettier**: Enforce consistent code formatting.

### CI/CD
- **GitHub Actions or Vercel**: Run lint, tests, and type checks on each PR before merge.

## 9. Conclusion and Overall Frontend Summary

This guide covers how we build Pinnacle Punch & Drill’s frontend: from the Next.js architecture, through our modern flat design, to the reusable component structure and offline support. Our focus on usability, accessibility, and performance ensures field employees and managers have a fast, reliable experience—whether online or offline. With clear styling tokens, solid state management, and robust testing in place, the frontend stays maintainable as the product grows.

By following these guidelines, every team member—designers, developers, or QA—will have a shared understanding of how to extend, maintain, and scale the frontend effectively.
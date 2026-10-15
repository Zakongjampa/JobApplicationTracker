# Job Application Tracker

A full-stack app to track job applications through a Kanban pipeline.

**Live demo:** https://your-cloudfront-url
**Demo video:** https://loom.com/...
**Test account:** demo@example.com / demo1234

## Screenshots
[3–4 images: board, dashboard, upload, mobile view]

## Features
- JWT auth (register/login)
- Create, edit, delete applications
- Kanban board with drag-and-drop status changes
- Full status change history per application
- Resume uploads via S3 presigned URLs (files never touch the API)
- Dashboard with counts and follow-ups due today
- Per-user data isolation enforced at the query level

## Tech Stack
**Backend:** Java 17, Spring Boot 3, Spring Security, Spring Data JPA, Flyway, PostgreSQL, AWS SDK v2 (S3)
**Frontend:** React 18, TypeScript, Vite, TanStack Query, React Router, Tailwind, @dnd-kit
**Infra:** AWS App Runner, RDS Postgres, S3, CloudFront

## Architecture
[Paste the diagram from section 3.1]

## Running Locally
### Backend
1. `createdb jobtracker`
2. Set env: `SPRING_DATASOURCE_URL`, `SPRING_DATASOURCE_USERNAME`, `SPRING_DATASOURCE_PASSWORD`, `JWT_SECRET`, `AWS_REGION`, `S3_BUCKET`
3. `./mvnw spring-boot:run`

### Frontend
1. `cd frontend && npm install`
2. Create `.env` with `VITE_API_URL=http://localhost:8080/api`
3. `npm run dev`

## API
See [API.md](./docs/API.md) or the full endpoint list in this README.

## Design Decisions
- **Presigned S3 uploads:** keeps the API stateless and avoids proxying large files through the JVM.
- **Status as an enum, not a table:** the pipeline is fixed; a table would be over-engineering.
- **Ownership in the query, not the controller:** `findByIdAndUserId` prevents IDOR by construction.
- **JWT without refresh tokens:** acceptable for a portfolio app; a production app would add short-lived access + rotating refresh tokens.
- **Flyway over `ddl-auto: update`:** schema changes are versioned and reviewable.

## What I'd Add Next
- Refresh tokens + logout-everywhere
- Email reminders (SES + scheduled Lambda)
- Resume parsing to auto-fill applications
- Team/shared boards with roles

## What I Cut and Why
- Email verification — not needed for the core UX, adds an SES dependency
- Real-time updates — WebSockets add deployment complexity disproportionate to value here

# Mobile Car Care 🚗

A full-stack web app for booking **mobile car care services** — the provider comes to the customer's location instead of the other way around.

## Documentation

For bilingual English-Arabic explanations, representative code, and 30 questions with answers, read the [Bilingual Guide and Questions](docs/Bilingual-Guide-and-Questions.pdf) or its [Markdown source](docs/Bilingual-Guide-and-Questions.md).


For a complete explanation of the architecture, setup, customer and admin workflows, API routes, data model, deployment steps, verification results, and known limitations, read the [Mobile CarCare Application Guide](docs/Mobile-CarCare-Application-Guide.pdf).

## Features

- Browse services & add-ons
- JWT auth (register / login / logout)
- Customer dashboard — manage vehicles & bookings
- Multi-step booking flow: service → vehicle → add-ons → location → date → time → review → confirm
- Backend-driven pricing & availability (no double-booking)
- Admin dashboard — services, add-ons, bookings, customers, calendar
- Responsive (360px–1440px) & SEO-friendly

## Tech Stack

| Layer    | Tech |
| -------- | ---- |
| Frontend | Next.js (App Router), React, TypeScript, Tailwind CSS |
| Backend  | NestJS, TypeScript, MongoDB, Mongoose |
| Auth     | JWT + bcrypt, role-based access |

## Structure

```
apps/
├── web/   → Next.js frontend
└── api/   → NestJS REST API
```

MongoDB is the single source of truth for pricing & availability.

## Getting Started

```bash
npm install

cp .env.example .env
cp apps/api/.env.example apps/api/.env
cp apps/web/.env.example apps/web/.env.local

npm run dev:api     # http://localhost:3001
npm run dev:web     # http://localhost:3000
```

The API seeds demo services, add-ons, and users on first run (credentials printed to console — dev only).

### Env Variables

| Variable | Where | Description |
| -------- | ----- | ----------- |
| `DATABASE_URL` | `apps/api/.env` | MongoDB connection string |
| `JWT_SECRET` | `apps/api/.env` | JWT signing secret |
| `JWT_EXPIRES_IN` | `apps/api/.env` | Token lifetime (e.g. `7d`) |
| `PORT` | `apps/api/.env` | Backend port |
| `CORS_ORIGINS` | `apps/api/.env` | Allowed origins (comma-separated) |
| `NEXT_PUBLIC_API_URL` | `apps/web/.env.local` | Backend base URL |

### Commands

```bash
npm run build       # build both apps
npm run lint         # lint both apps
npm run typecheck    # type-check both apps
npm run test         # run tests
```

## API Overview

| Method | Endpoint | Access | Description |
| ------ | -------- | ------ | ----------- |
| GET | `/health` | Public | Health check |
| POST | `/auth/register` | Public | Register customer |
| POST | `/auth/login` | Public | Login → JWT |
| GET | `/auth/me` | Auth | Current user |
| GET | `/services` | Public | Active services |
| GET | `/add-ons` | Public | Active add-ons |
| GET/POST/PATCH/DELETE | `/vehicles` | Customer | Manage own vehicles |
| GET | `/availability` | Public | Available slots |
| GET/POST | `/bookings` | Customer | Create / view bookings |
| POST | `/bookings/:id/cancel` | Customer | Cancel booking |
| GET | `/admin/*` | Admin | Dashboard, services, bookings, customers, calendar |

## How Booking Works

- Working hours: `09:00–18:00` (centrally configured)
- Backend computes open slots from working hours, service duration & existing bookings
- Backend always calculates final price — frontend totals aren't trusted
- Overlapping bookings return `409 Conflict`
- Customers can cancel `PENDING`/`CONFIRMED` bookings only

## Roadmap

- Real payment gateway
- Email/SMS notifications
- Technician assignment & tracking
- Multi-language support
- Advanced analytics

## License

Private project for demonstration purposes.

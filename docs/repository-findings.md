# Repository findings

## Repository and architecture

The repository is a private-workspace monorepo named `mobile-car-care` with two applications: `apps/web` is a Next.js 16 App Router frontend using React, TypeScript, Tailwind CSS, and a same-origin `/api/[...path]` runtime proxy; `apps/api` is a NestJS REST API using MongoDB/Mongoose. The API modules are users, auth, services, add-ons, vehicles, bookings, admin, notifications, contact, seed, and health.

## Runtime flow

The browser uses `apps/web/src/lib/api.ts`. In the browser, requests go to `/api`; on the server, requests use `API_URL`, then `NEXT_PUBLIC_API_URL`, then `http://localhost:3001`. Auth tokens are stored in localStorage under `mcc_token` and sent as Bearer tokens. The Next.js proxy forwards methods, headers, body, and query strings to the API and returns a 502 JSON response if the API is unreachable.

Nest bootstrap loads environment configuration, connects to MongoDB, enables CORS, and applies a global ValidationPipe with whitelist, forbidden extra properties, transformation, and implicit conversion. Production requires `DATABASE_URL`; local development defaults to `mongodb://127.0.0.1:27017/mobile-car-care`. The API listens on `PORT` or 3001. Vercel uses `apps/api/serverless.ts` to initialize and cache a Nest HTTP adapter.

## Authentication and authorization

Registration and login issue JWTs. Registration always creates a CUSTOMER and rejects attempted ADMIN registration. Passwords are handled through bcrypt in UsersService. JwtStrategy reads the Bearer token, verifies the configured secret, reloads the user, and attaches id/email/name/role to the request. Admin routes use JwtAuthGuard plus RolesGuard and require ADMIN. Logout is stateless: it returns success and the frontend removes the local token. Password reset generates a one-hour token and returns it in the response because no email service is configured.

## Booking behavior

Customers can manage only their own vehicles and bookings. A booking validates that the vehicle belongs to the customer, all selected services and add-ons exist and are active, sums durations and prices from MongoDB, validates a non-past date and a 09:00–18:00 working window, computes the end time, and rejects overlap with any non-cancelled booking using HTTP 409. The stored booking is PENDING with PAYMENT PENDING. Multi-service selections are stored in `services`, while legacy/top-level `serviceId` and `addOnIds` mirror the first selection. Customer cancellation is allowed for non-completed, non-cancelled bookings; cancellation and new bookings notify admins, and admin status changes notify customers.

## Main data models

User stores name, lowercase email, bcrypt password, role, and optional password-reset fields. Vehicle stores customer ownership and car details. CarService stores name, slug, description, basePrice, duration, imageUrl, and isActive. AddOn stores name, description, price, and isActive. Booking stores customer, vehicle, service/add-ons, service line items, date/time, duration, subtotal, total, status, payment status, and location. Notification stores recipient, type, title, message, data, read state, and timestamps. ContactMessage stores name, email, message, read state, and timestamps.

## Frontend journeys

Public pages include home, services, service detail, about, how-it-works, FAQ, contact, login, register, and forgot-password. The customer dashboard provides profile, vehicles, bookings/list/detail, notifications, and a multi-step booking flow: service selection, vehicle, add-ons, location, date, time, review, and confirmation. The admin dashboard provides metrics, services CRUD, add-ons CRUD, booking list/detail/status changes, customers, messages, calendar, and notifications. `RequireAuth` and `RequireRole` route guards protect customer/admin areas.

## Validation results

`npm run build` passed for both API and web. API tests passed: 2 suites, 6 tests. Root `npm run test` exits non-zero because the web workspace has no `test` script. `npm run typecheck` exits non-zero because `apps/web/src/app/page.tsx` imports four binary files directly from `public/images/services`; TypeScript reports missing module/type declarations for those image imports even though the corresponding files exist in the working tree. `npm install` reported 10 dependency audit vulnerabilities (3 moderate, 7 high); this is recorded as an observation, not automatically fixed.

## Documentation caveats to explain

There is no real payment gateway, email/SMS service, technician assignment, or tracking. Logout does not revoke an already-issued JWT server-side. Availability and booking conflict checks are application-level queries rather than a database uniqueness/transaction mechanism, so concurrent requests would deserve additional production hardening. The PDF should explain local setup, environment variables, customer flow, admin flow, API routes, data model, deployment, validation results, and known limitations.

## Source reference

Repository: https://github.com/Abdobeaih/Car-Wash

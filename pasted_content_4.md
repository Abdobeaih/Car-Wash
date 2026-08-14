# TASKS.md

# Mobile Car Care — Sequential Implementation Tasks

## GLOBAL EXECUTION RULE

Execute tasks strictly in this order:

TASK 1
↓
TASK 2
↓
TASK 3
↓
TASK 4
↓
TASK 5
↓
TASK 6

The agent must automatically continue to the next task after the current task passes validation.

Do NOT ask the user for approval between tasks.

Only stop if a genuine blocker requires user input.

---

# TASK 1 — PROJECT FOUNDATION

## Objective

Create the initial full-stack project foundation.

---

## Requirements

### Frontend

Create/configure:

apps/web

Using:

- Next.js.
- React.
- TypeScript.
- Tailwind CSS.
- App Router.

Create:

- Root layout.
- Global styles.
- Basic metadata.
- Navigation.
- Footer.
- Responsive structure.

---

### Homepage

Create:

/

Include:

- Hero section.
- Primary Book a Service CTA.
- Services preview.
- How It Works.
- Why Choose Us.
- Final CTA.
- Footer.

Do not invent real company information.

---

### Navigation

Desktop:

- Logo.
- Services.
- How It Works.
- About.
- Contact.
- Login.
- Book a Service.

Mobile:

- Responsive menu.
- Accessible controls.
- No horizontal overflow.

---

### Backend

Create/configure:

apps/api

Using:

- NestJS.
- TypeScript.
- MongoDB.
- Mongoose.

Configure:

- Bootstrap.
- Environment variables.
- MongoDB connection.
- CORS.
- Global validation.
- Basic error handling.

---

### Health Endpoint

Create:

GET /health

Response:

{
  "status": "ok"
}

---

### Environment

Create:

.env.example

Include appropriate variables such as:

DATABASE_URL
JWT_SECRET
NEXT_PUBLIC_API_URL

Never use real secrets.

---

### README

Document:

- Project overview.
- Stack.
- Architecture.
- Installation.
- Environment variables.
- Development commands.

---

## DO NOT IMPLEMENT

Do not implement:

- Authentication.
- Vehicles.
- Booking.
- Calendar.
- Admin dashboard.
- Payments.

---

## VALIDATION

Run:

- TypeScript.
- ESLint.
- Tests.
- Frontend build.
- Backend build.

Fix every error.

Repeat validation until clean.

---

## TASK 1 COMPLETE WHEN

- Frontend runs.
- Backend runs.
- MongoDB connection works.
- /health works.
- Homepage works.
- Navigation works.
- Footer works.
- Responsive layout works.
- .env.example exists.
- README exists.
- TypeScript passes.
- ESLint passes.
- Tests pass.
- Frontend build passes.
- Backend build passes.

---

## AFTER TASK 1

Report:

TASK 1 COMPLETED

Then automatically start:

TASK 2 — AUTHENTICATION & AUTHORIZATION

Do not wait for user approval.

---

# TASK 2 — AUTHENTICATION & AUTHORIZATION

## Objective

Implement secure authentication and role-based authorization.

---

## User Model

Create:

User

Fields:

name
email
password
role
createdAt
updatedAt

Roles:

CUSTOMER
ADMIN

Email must be unique.

---

## Password Security

Use secure password hashing.

Never:

- Store plaintext passwords.
- Return passwords.
- Expose password hashes.

---

## Register

Create:

POST /auth/register

Validate:

- Name.
- Email.
- Password.
- Duplicate email.

---

## Login

Create:

POST /auth/login

Validate:

- Email.
- Password.
- Invalid credentials.

---

## JWT

Implement JWT authentication.

JWT secret must come from environment variables.

Never hardcode it.

---

## Auth APIs

POST /auth/register
POST /auth/login
POST /auth/logout
GET /auth/me

---

## Guards

Implement:

- Authentication guard.
- Role guard.

---

## Frontend Pages

Create:

/login
/register
/dashboard
/admin

---

## Authorization

CUSTOMER can access:

/dashboard

ADMIN can access:

/admin

Backend authorization is mandatory.

---

## Logout

Implement secure logout according to the selected JWT strategy.

---

## Tests

Test:

- Registration.
- Duplicate email.
- Login.
- Invalid credentials.
- JWT.
- /auth/me.
- Logout.
- Protected routes.
- Admin authorization.
- Customer denied admin access.

---

## DO NOT IMPLEMENT

Do not implement:

- Vehicles.
- Services management.
- Booking.
- Calendar.
- Admin management features.

---

## VALIDATION

Run:

- TypeScript.
- ESLint.
- Tests.
- Frontend build.
- Backend build.

Fix every error.

Repeat until clean.

---

## TASK 2 COMPLETE WHEN

- Registration works.
- Login works.
- Logout works.
- JWT works.
- /auth/me works.
- Password hashing works.
- Protected routes work.
- Role authorization works.
- Validation works.
- Tests pass.
- TypeScript passes.
- ESLint passes.
- Frontend build passes.
- Backend build passes.

---

## AFTER TASK 2

Report:

TASK 2 COMPLETED

Then automatically start:

TASK 3 — SERVICES, ADD-ONS & VEHICLES

---

# TASK 3 — SERVICES, ADD-ONS & VEHICLES

## Objective

Implement service browsing, add-ons and customer vehicle management.

---

## Service Model

Fields:

name
slug
description
image
basePrice
duration
isActive
createdAt
updatedAt

---

## Service APIs

GET /services
GET /services/:slug

Only active services should be publicly available.

---

## Service Pages

Create:

/services
/services/[slug]

Display:

- Name.
- Description.
- Image.
- Price.
- Duration.
- Add-ons.
- Booking CTA.

---

## Add-on Model

Fields:

name
description
price
isActive
createdAt
updatedAt

---

## Add-on API

GET /add-ons

---

## Vehicle Model

Fields:

userId
brand
model
year
color
plateNumber
vehicleType
createdAt
updatedAt

Vehicle types:

SEDAN
SUV
PICKUP
LUXURY

---

## Vehicle APIs

GET /vehicles
POST /vehicles
GET /vehicles/:id
PATCH /vehicles/:id
DELETE /vehicles/:id

---

## Ownership

Customers can only access their own vehicles.

Backend ownership validation is mandatory.

---

## Vehicle UI

Create:

/dashboard/vehicles

Features:

- List vehicles.
- Add vehicle.
- Edit vehicle.
- Delete vehicle.
- Loading state.
- Empty state.
- Error state.
- Success feedback.

---

## Seed Data

Services:

Exterior Car Wash
Interior Cleaning
Full Car Detailing
Premium Detailing

Add-ons:

Tire Cleaning
Engine Bay Cleaning
Leather Conditioning
Odor Treatment

Development may contain:

- Admin user.
- Customer user.

Never expose credentials in frontend source.

---

## Pricing

Frontend must use backend-provided prices.

Do not hardcode prices inside UI components.

---

## Responsive

Test:

360px
390px
768px
1024px
1440px

---

## DO NOT IMPLEMENT

Do not implement:

- Booking.
- Calendar.
- Admin dashboard.
- Payments.
- Technician system.

---

## TESTING

Test:

- Services.
- Service details.
- Active filtering.
- Add-ons.
- Vehicle CRUD.
- Ownership.
- Validation.
- Authentication requirements.

---

## VALIDATION

Run:

- TypeScript.
- ESLint.
- Tests.
- Frontend build.
- Backend build.

Fix every error.

Repeat until clean.

---

## TASK 3 COMPLETE WHEN

- Services work.
- Service details work.
- Add-ons work.
- Vehicle CRUD works.
- Ownership protection works.
- Seed data works.
- Forms work.
- Responsive UI works.
- Validation works.
- Tests pass.
- TypeScript passes.
- ESLint passes.
- Frontend build passes.
- Backend build passes.

---

## AFTER TASK 3

Report:

TASK 3 COMPLETED

Then automatically start:

TASK 4 — BOOKING & CALENDAR

---

# TASK 4 — BOOKING & CALENDAR

## Objective

Implement the core booking and availability system.

---

## Booking Model

Fields:

customerId
vehicleId
serviceId
addOnIds
date
startTime
endTime
duration
subtotal
total
status
paymentStatus
location
createdAt
updatedAt

---

## Booking Status

PENDING
CONFIRMED
COMPLETED
CANCELLED

---

## Payment Status

PENDING
PAID

No real payment gateway.

---

## Working Hours

Initial:

09:00 - 18:00

Keep this configuration centralized.

---

## Availability API

Create:

GET /availability

Inputs:

date
serviceId

The backend determines availability.

---

## Availability Logic

Availability depends on:

Working Hours
+
Service Duration
+
Existing Bookings

Example:

Working hours:

09:00 - 18:00

For a 60-minute service:

09:00
10:00
11:00
12:00
13:00
14:00
15:00
16:00
17:00

If 11:00 - 12:00 is booked:

11:00 must not be available.

---

## Booking Creation

Create:

POST /bookings

Validate:

- Authentication.
- Vehicle ownership.
- Service existence.
- Service active status.
- Add-ons.
- Date.
- Time.
- Working hours.
- Duration.
- Existing bookings.

---

## Backend Pricing

Calculate:

service.basePrice
+
sum(addOn.price)

Never trust frontend totals.

---

## Double Booking

Prevent overlapping bookings.

Before creating:

1. Check existing bookings.
2. Detect time overlap.
3. Reject conflict.

Use:

409 Conflict

where appropriate.

---

## Booking APIs

POST /bookings
GET /bookings
GET /bookings/:id
POST /bookings/:id/cancel

Customers can only access their own bookings.

---

## Booking UI

Create:

/book
/dashboard/bookings
/dashboard/bookings/[id]

---

## Booking Flow

Service
↓
Vehicle
↓
Add-ons
↓
Location
↓
Date
↓
Available Time
↓
Review
↓
Confirm
↓
Booking Details

---

## Location

Collect:

country
city
address
latitude
longitude
notes

No map integration is required.

---

## Calendar

Calendar must:

- Select date.
- Display availability.
- Display available slots.
- Disable unavailable slots.
- Show loading.
- Show errors.
- Work on mobile.
- Work on desktop.

Backend is the source of truth.

---

## Booking Confirmation

Display:

- Booking ID.
- Service.
- Vehicle.
- Add-ons.
- Date.
- Time.
- Location.
- Price.
- Status.

---

## Cancellation

Allow cancellation for:

PENDING
CONFIRMED

Do not allow cancellation for:

COMPLETED
CANCELLED

---

## Dashboard

Show:

- Upcoming booking.
- Recent bookings.
- Vehicles.
- Book Service CTA.

---

## TESTING

Test:

- Availability.
- Pricing.
- Booking creation.
- Vehicle ownership.
- Service validation.
- Add-on validation.
- Invalid time.
- Outside working hours.
- Double booking.
- Booking ownership.
- Cancellation.

---

## E2E FLOW

Register
↓
Login
↓
Add Vehicle
↓
Select Service
↓
Select Add-ons
↓
Select Location
↓
Select Date
↓
Select Time
↓
Review
↓
Confirm
↓
View Booking
↓
Cancel

---

## DO NOT IMPLEMENT

Do not implement:

- Payments.
- GPS.
- Technician tracking.
- WebSockets.
- Notification infrastructure.
- Subscriptions.

---

## VALIDATION

Run:

- TypeScript.
- ESLint.
- Unit tests.
- Integration tests.
- E2E tests where configured.
- Frontend build.
- Backend build.

Fix every error.

Repeat until clean.

---

## TASK 4 COMPLETE WHEN

- Availability works.
- Calendar works.
- Booking works.
- Backend calculates prices.
- Double booking is prevented.
- Ownership is enforced.
- Cancellation works.
- Full booking flow works.
- Mobile booking works.
- Critical tests pass.
- TypeScript passes.
- ESLint passes.
- Frontend build passes.
- Backend build passes.

---

## AFTER TASK 4

Report:

TASK 4 COMPLETED

Then automatically start:

TASK 5 — ADMIN DASHBOARD

---

# TASK 5 — ADMIN DASHBOARD

## Objective

Build a simple professional admin dashboard.

---

## Dashboard

Create:

/admin

Metrics:

- Total Bookings.
- Pending Bookings.
- Confirmed Bookings.
- Completed Bookings.
- Customers.
- Revenue.

Keep analytics simple.

---

## Service Management

Create:

/admin/services

Admin can:

- Create.
- Edit.
- Activate.
- Deactivate.

---

## Add-on Management

Create:

/admin/add-ons

Admin can:

- Create.
- Edit.
- Activate.
- Deactivate.
- Delete.

---

## Booking Management

Create:

/admin/bookings

Admin can:

- View.
- Search.
- Filter.
- View details.
- Change status.

---

## Customer Management

Create:

/admin/customers

Display:

- Name.
- Email.
- Booking count.
- Registration date.

Never expose passwords.

---

## Admin Calendar

Create:

/admin/calendar

Display bookings using:

- Day.
- Week.
- Month.

Keep it simple.

---

## Authorization

Every admin API endpoint must verify:

ADMIN

Frontend route protection alone is not enough.

---

## Responsive

Desktop:

- Sidebar.

Mobile:

- Responsive navigation.

Tables must remain usable on small screens.

---

## UX

Include:

- Loading.
- Empty.
- Error.
- Success.
- Confirmation for destructive actions.
- Status indicators.

---

## DO NOT IMPLEMENT

Do not implement:

- Technician management.
- GPS.
- Live tracking.
- Payment gateway.
- Advanced analytics.
- Notification infrastructure.

---

## TESTING

Test:

- Admin login.
- Admin authorization.
- Customer denied admin.
- Service management.
- Add-on management.
- Booking management.
- Customer management.
- Calendar.
- Status changes.

---

## VALIDATION

Run:

- TypeScript.
- ESLint.
- Tests.
- Frontend build.
- Backend build.

Fix every error.

Repeat until clean.

---

## TASK 5 COMPLETE WHEN

- Dashboard works.
- Service management works.
- Add-on management works.
- Booking management works.
- Customer management works.
- Calendar works.
- Authorization works.
- Responsive design works.
- Tests pass.
- TypeScript passes.
- ESLint passes.
- Frontend build passes.
- Backend build passes.

---

## AFTER TASK 5

Report:

TASK 5 COMPLETED

Then automatically start:

TASK 6 — SEO, PERFORMANCE, SECURITY & FINAL QA

---

# TASK 6 — SEO, PERFORMANCE, SECURITY & FINAL QA

## Objective

Finalize and audit the complete application.

Do not introduce major new features.

---

# SEO AUDIT

Optimize public pages:

/
 /services
 /services/[slug]
 /about
 /how-it-works
 /contact
 /faq

Implement:

- Unique titles.
- Meta descriptions.
- Open Graph metadata.
- Canonical URLs where appropriate.
- Semantic HTML.
- Sitemap.
- Robots configuration.
- Structured data where appropriate.

---

# DYNAMIC SEO

Service pages must have dynamic metadata.

---

# PRIVATE PAGES

Dashboard and admin pages must not be indexed.

Use appropriate noindex configuration.

---

# STRUCTURED DATA

Where appropriate use:

- Service.
- FAQPage.
- LocalBusiness.

Do not invent real business information.

---

# INTERNATIONALIZATION AUDIT

Search for unnecessary hardcoded assumptions such as:

Saudi Arabia
SAR
+966
Riyadh
Asia/Riyadh

Remove unnecessary country-specific assumptions.

The application must remain internationally configurable.

---

# RESPONSIVE AUDIT

Test:

360px
390px
768px
1024px
1440px

Review:

- Header.
- Footer.
- Homepage.
- Services.
- Service details.
- Login.
- Register.
- Dashboard.
- Vehicles.
- Booking.
- Calendar.
- Booking details.
- Admin dashboard.
- Admin tables.
- Admin calendar.

Fix:

- Horizontal overflow.
- Broken layouts.
- Text overflow.
- Mobile navigation.
- Touch target issues.
- Spacing issues.

---

# UX AUDIT

Verify:

- Loading states.
- Empty states.
- Error states.
- Success feedback.
- Validation.
- Disabled buttons.
- Confirmation dialogs.
- API errors.

---

# PERFORMANCE AUDIT

Review:

- Images.
- Client Components.
- Server Components.
- API requests.
- Duplicate requests.
- Database queries.
- Database indexes.
- Bundle size.
- Large components.
- Dependencies.

Optimize only where justified.

---

# SECURITY AUDIT

Review:

- Authentication.
- Authorization.
- JWT.
- Password hashing.
- Input validation.
- CORS.
- Environment variables.
- Ownership checks.
- Admin permissions.
- Error handling.

Fix critical issues.

---

# API AUDIT

Review:

/auth
/services
/add-ons
/vehicles
/bookings
/availability
/admin

Verify:

- Authentication.
- Authorization.
- Validation.
- Error handling.
- HTTP status codes.

---

# DATABASE AUDIT

Review:

- Required fields.
- Unique constraints.
- Indexes.
- Relationships.
- Query efficiency.

Do not unnecessarily redesign the database.

---

# CUSTOMER E2E

Run:

Homepage
↓
Services
↓
Service Details
↓
Register
↓
Login
↓
Add Vehicle
↓
Select Service
↓
Select Add-ons
↓
Select Location
↓
Select Date
↓
Select Time
↓
Review
↓
Confirm Booking
↓
Booking Details

---

# ADMIN E2E

Run:

Admin Login
↓
Dashboard
↓
Services
↓
Add-ons
↓
Bookings
↓
Customers
↓
Calendar

---

# CRITICAL BUSINESS TESTS

Verify:

- Authentication.
- Authorization.
- Vehicle ownership.
- Price calculation.
- Availability.
- Double booking prevention.
- Booking creation.
- Cancellation.
- Admin permissions.

---

# PRODUCTION BUILDS

Run:

- Frontend production build.
- Backend production build.

Fix all critical errors.

---

# CODE CLEANUP

Remove:

- Debug logs.
- Dead code.
- Unused imports.
- Duplicate logic.
- Temporary code.
- Unnecessary dependencies.

Do not perform unrelated refactoring.

---

# README

README must contain:

- Project overview.
- Features.
- Architecture.
- Technology stack.
- Installation.
- Environment variables.
- Development commands.
- API overview.
- Authentication.
- Booking flow.
- Availability logic.
- Admin functionality.
- Testing.
- Future improvements.

---

# FINAL SCOPE REVIEW

Do NOT add:

- Technician system.
- GPS.
- Live tracking.
- Payment gateway.
- Subscriptions.
- Notification infrastructure.
- WebSockets.
- Redis.
- Microservices.
- GraphQL.

---

# FINAL DEFINITION OF DONE

The project is complete when:

- Customer flow works.
- Admin flow works.
- Authentication works.
- Authorization works.
- Vehicles work.
- Services work.
- Add-ons work.
- Booking works.
- Calendar works.
- Availability works.
- Double booking protection works.
- Cancellation works.
- SEO works.
- Sitemap works.
- Robots works.
- Responsive UI works.
- Security review passes.
- TypeScript passes.
- ESLint passes.
- Tests pass.
- Frontend production build passes.
- Backend production build passes.
- README is complete.

---

# PROJECT COMPLETION

After all validations pass:

Report:

PROJECT COMPLETED

Include:

- Main implemented features.
- Architecture.
- Validation results.
- Any remaining non-critical issues.
- Possible future improvements.

Do NOT start additional features automatically.

# END OF TASKS.md
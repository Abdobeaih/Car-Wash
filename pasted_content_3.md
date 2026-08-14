# PROJECT_SPEC.md

# Mobile Car Care — Project Specification

## 1. PROJECT OVERVIEW

Mobile Car Care is a responsive web application that allows customers to book professional mobile car care services.

The service provider travels to the customer's selected location.

The customer can:

- Browse services.
- Select a vehicle.
- Select add-ons.
- Select location.
- Select date.
- Select available time.
- Review the booking.
- Confirm the booking.
- Manage bookings.

The application must be suitable for multiple countries.

Do not hardcode the application to Saudi Arabia or any specific country.

---

# 2. MAIN CUSTOMER FLOW

Homepage
↓
Services
↓
Service Details
↓
Select Vehicle
↓
Select Add-ons
↓
Select Location
↓
Select Date
↓
Select Available Time
↓
Review Booking
↓
Confirm Booking
↓
Manage Booking

---

# 3. USERS

## CUSTOMER

Customer can:

- Register.
- Login.
- Logout.
- View profile.
- Add vehicles.
- Edit vehicles.
- Delete vehicles.
- Browse services.
- Select add-ons.
- Check availability.
- Create bookings.
- View bookings.
- View booking details.
- Cancel eligible bookings.

---

## ADMIN

Admin can:

- Login.
- View dashboard.
- Manage services.
- Manage add-ons.
- Manage bookings.
- Manage customers.
- View calendar.
- Update booking status.

---

# 4. TECHNOLOGY

## FRONTEND

- Next.js
- React
- TypeScript
- Tailwind CSS

Use:

Next.js App Router.

---

## BACKEND

- NestJS
- TypeScript
- MongoDB
- Mongoose

Backend exposes REST APIs.

---

# 5. PROJECT STRUCTURE

Recommended:

/
├── AGENTS.md
├── PROJECT_SPEC.md
├── TASKS.md
├── README.md
│
└── apps/
    ├── web/
    └── api/

---

# 6. USER MODEL

User fields:

_id
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

# 7. SERVICE MODEL

Service fields:

_id
name
slug
description
image
basePrice
duration
isActive
createdAt
updatedAt

Duration is measured in minutes.

---

# 8. ADD-ON MODEL

AddOn fields:

_id
name
description
price
isActive
createdAt
updatedAt

---

# 9. VEHICLE MODEL

Vehicle fields:

_id
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

# 10. BOOKING MODEL

Booking fields:

_id
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

# 11. BOOKING STATUS

PENDING
CONFIRMED
COMPLETED
CANCELLED

---

# 12. PAYMENT STATUS

PENDING
PAID

No real payment gateway is required in version one.

---

# 13. LOCATION

Booking location:

country
city
address
latitude
longitude
notes

Do not hardcode a specific country.

---

# 14. INITIAL SERVICES

Exterior Car Wash
Interior Cleaning
Full Car Detailing
Premium Detailing

Each service includes:

- Name.
- Description.
- Image.
- Price.
- Duration.
- Active status.

Inactive services cannot be booked.

---

# 15. INITIAL ADD-ONS

Tire Cleaning
Engine Bay Cleaning
Leather Conditioning
Odor Treatment

Inactive add-ons cannot be selected.

---

# 16. PUBLIC PAGES

/
 /services
 /services/[slug]
 /about
 /how-it-works
 /contact
 /faq

---

# 17. AUTH PAGES

/login
/register

---

# 18. CUSTOMER PAGES

/dashboard
/dashboard/vehicles
/dashboard/bookings
/dashboard/bookings/[id]
/book

---

# 19. ADMIN PAGES

/admin
/admin/services
/admin/add-ons
/admin/bookings
/admin/customers
/admin/calendar

---

# 20. AUTH APIs

POST /auth/register
POST /auth/login
POST /auth/logout
GET /auth/me

---

# 21. SERVICE APIs

GET /services
GET /services/:slug

---

# 22. ADD-ON API

GET /add-ons

---

# 23. VEHICLE APIs

GET /vehicles
POST /vehicles
GET /vehicles/:id
PATCH /vehicles/:id
DELETE /vehicles/:id

Customers can only access their own vehicles.

---

# 24. AVAILABILITY API

GET /availability

Inputs:

date
serviceId

The backend determines availability.

Frontend is not the source of truth.

---

# 25. WORKING HOURS

Initial working hours:

09:00 - 18:00

Working hours should be centrally configurable.

---

# 26. AVAILABILITY LOGIC

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

If 11:00 - 12:00 is booked, it must not appear as available.

---

# 27. DOUBLE BOOKING

Backend must prevent overlapping bookings.

Before creating a booking:

1. Validate service.
2. Validate duration.
3. Validate date.
4. Validate time.
5. Validate working hours.
6. Check existing bookings.
7. Detect overlap.
8. Reject conflict.

Use HTTP 409 Conflict for unavailable slots where appropriate.

---

# 28. BOOKING APIs

POST /bookings
GET /bookings
GET /bookings/:id
POST /bookings/:id/cancel

Customers can only access their own bookings.

---

# 29. PRICING

Backend calculates:

service.basePrice
+
sum(addOn.price)

Frontend totals are never trusted.

Backend retrieves current prices from the database.

---

# 30. BOOKING FLOW

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

Customer must be authenticated before final booking creation.

---

# 31. BOOKING DETAILS

Display:

- Booking ID.
- Service.
- Vehicle.
- Add-ons.
- Date.
- Time.
- Location.
- Duration.
- Price.
- Status.

---

# 32. CANCELLATION

Customer can cancel:

PENDING
CONFIRMED

Customer cannot cancel:

COMPLETED
CANCELLED

---

# 33. CUSTOMER DASHBOARD

Display:

- Upcoming booking.
- Recent bookings.
- Vehicles.
- Book Service CTA.

---

# 34. ADMIN DASHBOARD

Metrics:

- Total Bookings.
- Pending Bookings.
- Confirmed Bookings.
- Completed Bookings.
- Customers.
- Revenue.

Keep analytics simple.

---

# 35. ADMIN SERVICE MANAGEMENT

Admin can:

- Create service.
- Edit service.
- Activate service.
- Deactivate service.

---

# 36. ADMIN ADD-ON MANAGEMENT

Admin can:

- Create add-on.
- Edit add-on.
- Activate add-on.
- Deactivate add-on.
- Delete add-on.

---

# 37. ADMIN BOOKING MANAGEMENT

Admin can:

- View bookings.
- Search bookings.
- Filter bookings.
- View booking details.
- Change booking status.

---

# 38. ADMIN CUSTOMER MANAGEMENT

Admin can:

- View customers.
- Search customers.
- View booking count.

Never expose passwords.

---

# 39. ADMIN CALENDAR

Admin can view bookings using:

- Day.
- Week.
- Month.

Keep the calendar simple.

---

# 40. RESPONSIVE DESIGN

The application must work at:

360px
390px
768px
1024px
1440px

The booking experience is especially important on mobile.

---

# 41. ACCESSIBILITY

Use:

- Semantic HTML.
- Proper labels.
- Keyboard navigation.
- Visible focus.
- Accessible validation.
- Good contrast.
- Meaningful alt text.

---

# 42. SEO

Public pages must have:

- Unique titles.
- Meta descriptions.
- Open Graph metadata.
- Semantic headings.
- Canonical URLs where appropriate.
- Sitemap.
- Robots configuration.
- Structured data where appropriate.

Service pages should have dynamic metadata.

Private pages must not be indexed.

---

# 43. PERFORMANCE

Prefer:

- Server Components.
- Optimized images.
- Minimal client JavaScript.
- Efficient API calls.
- Efficient database queries.

Do not introduce unnecessary infrastructure.

---

# 44. SECURITY

Must include:

- Password hashing.
- JWT authentication.
- Role-based authorization.
- Input validation.
- Ownership checks.
- Environment variables.
- CORS.
- Safe error handling.

---

# 45. SEED DATA

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

Development environment may contain:

- Admin user.
- Customer user.

Never expose credentials in frontend source.

---

# 46. OUT OF SCOPE

Do NOT implement:

- Payment gateway.
- Technician management.
- GPS tracking.
- Live technician tracking.
- Subscriptions.
- Loyalty system.
- Advanced analytics.
- Real-time notifications.
- WebSockets.
- Redis.
- GraphQL.
- Microservices.

---

# 47. PROJECT GOAL

The final application must be:

- Professional.
- Responsive.
- Secure.
- SEO-friendly.
- Maintainable.
- Internationally configurable.
- Full-stack.
- Simple enough to explain in a technical interview.

Avoid overengineering.

# END OF PROJECT_SPEC.md
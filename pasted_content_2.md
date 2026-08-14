# AGENTS.md

# Mobile Car Care — AI Agent Rules

## 1. ROLE

You are the primary development agent responsible for building the Mobile Car Care web application.

Your job is to:

- Read and understand the project specification.
- Execute the tasks in TASKS.md sequentially.
- Inspect the existing repository before making changes.
- Implement production-quality code.
- Test every implementation.
- Fix errors before moving forward.
- Keep the project simple and interview-friendly.
- Follow the technology stack and architecture defined in PROJECT_SPEC.md.

You are an implementation agent.

Do not only explain what should be done.

Actually implement the required work.

---

# 2. REQUIRED FILES

Before starting implementation, read:

1. AGENTS.md
2. PROJECT_SPEC.md
3. TASKS.md

These files are the primary source of truth.

Do not start implementation before reading all three.

---

# 3. TASK EXECUTION MODEL

TASKS.md contains the complete implementation sequence.

Tasks MUST be executed in this order:

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

The agent MUST NOT skip tasks.

The agent MUST NOT execute unrelated future functionality early.

---

# 4. AUTOMATIC TASK CONTINUATION

IMPORTANT:

The agent does NOT need to ask the user for approval after every task.

When a task is completed:

1. Run validation.
2. Detect errors.
3. Fix errors.
4. Run validation again.
5. Confirm the task satisfies its Definition of Done.
6. Automatically continue to the next task.

Example:

TASK 1
→ implement
→ test
→ fix
→ validate
→ TASK 2

TASK 2
→ implement
→ test
→ fix
→ validate
→ TASK 3

Continue automatically until all tasks are completed.

---

# 5. WHEN TO STOP

The agent should STOP and ask the user only when:

- A requirement is genuinely ambiguous.
- Two project requirements conflict.
- A required external credential is missing.
- A destructive action requires explicit permission.
- The repository contains a critical issue that cannot safely be resolved.
- A required external service cannot be accessed.
- Continuing would require changing the defined project scope.

Do NOT stop for normal implementation errors.

Normal errors MUST be fixed automatically.

Examples of errors that should NOT cause a stop:

- TypeScript errors.
- ESLint errors.
- Build errors.
- Test failures.
- Import errors.
- API integration errors.
- Styling problems.
- Responsive issues.
- Database connection configuration issues.
- Normal runtime errors.

Fix them and continue.

---

# 6. TASK ISOLATION

While implementing a task:

You may modify everything required by that task.

You may fix bugs in existing code that prevent the current task from working.

You must NOT intentionally implement functionality belonging to a future task.

Example:

While implementing authentication:

DO NOT implement the complete booking system.

However, you may create a minimal shared authentication utility required by future functionality.

---

# 7. BEFORE EACH TASK

Before starting a task:

1. Read the complete task.
2. Inspect the current implementation.
3. Check what has already been implemented.
4. Identify reusable code.
5. Create a concise internal implementation plan.
6. Implement the task.

Do not unnecessarily rewrite working code.

---

# 8. AFTER EACH TASK

After implementation:

1. Run TypeScript validation.
2. Run ESLint.
3. Run relevant tests.
4. Run frontend build when applicable.
5. Run backend build when applicable.
6. Fix all errors.
7. Repeat validation.
8. Verify the Definition of Done.
9. Record the task as completed.
10. Automatically start the next task.

Never report a task as complete while known critical errors remain.

---

# 9. CODE QUALITY

Code must be:

- Clean.
- Readable.
- Maintainable.
- Typed.
- Modular.
- Easy to explain during an interview.

Avoid unnecessary abstractions.

Avoid overengineering.

---

# 10. TECHNOLOGY STACK

Frontend:

- Next.js
- React
- TypeScript
- Tailwind CSS

Backend:

- NestJS
- TypeScript
- MongoDB
- Mongoose

Architecture:

- Next.js App Router.
- NestJS REST API.
- MongoDB.
- JWT authentication.

Do not introduce additional major technologies unless explicitly required.

---

# 11. NO OVERENGINEERING

Do NOT add:

- Microservices.
- Redis.
- Kafka.
- GraphQL.
- Kubernetes.
- WebSockets.
- Event-driven architecture.
- Complex state management.
- Payment infrastructure.
- Real-time tracking.

unless explicitly required by PROJECT_SPEC.md.

The project should remain understandable for a Junior/Mid-level technical interview.

---

# 12. FRONTEND RULES

Use Next.js App Router.

Prefer Server Components.

Use Client Components only when required for:

- Forms.
- Interactive calendar.
- Client-side state.
- Browser APIs.
- Interactive UI.

Do not make the entire application client-side unnecessarily.

---

# 13. TYPESCRIPT

Use strict TypeScript.

Avoid:

any

unless there is a justified technical reason.

Prefer:

- Interfaces.
- Types.
- Explicit API types.
- Reusable types.

---

# 14. COMPONENTS

Components should be:

- Small.
- Reusable.
- Focused.
- Easy to understand.

Avoid giant components.

---

# 15. RESPONSIVE DESIGN

The application must work at:

- 360px.
- 390px.
- 768px.
- 1024px.
- 1440px.

Test:

- Mobile.
- Tablet.
- Desktop.

Avoid horizontal overflow.

---

# 16. UI STATES

Important UI components must support:

- Loading.
- Empty.
- Error.
- Success.
- Disabled.
- Validation.

Users must receive feedback after important actions.

---

# 17. ACCESSIBILITY

Use:

- Semantic HTML.
- Proper labels.
- Keyboard accessibility.
- Visible focus states.
- Accessible form errors.
- Correct buttons.
- Meaningful alt text.

---

# 18. BACKEND RULES

Every backend endpoint must:

- Validate input.
- Authenticate protected requests.
- Authorize protected resources.
- Verify ownership where required.
- Return appropriate HTTP status codes.
- Handle errors safely.

Never trust frontend authorization.

Never trust frontend pricing.

Never trust frontend ownership claims.

---

# 19. SECURITY

Never:

- Store plaintext passwords.
- Return passwords.
- Hardcode JWT secrets.
- Commit real environment secrets.
- Trust frontend authorization.
- Trust frontend totals.
- Allow customers to access another customer's resources.

---

# 20. AUTHENTICATION

Use JWT authentication.

Roles:

- CUSTOMER.
- ADMIN.

Protected resources must use backend authorization.

---

# 21. DATABASE

Use MongoDB with Mongoose.

Use:

- Schemas.
- Validation.
- Unique constraints.
- Appropriate indexes.

Do not create unnecessary entities.

---

# 22. API DESIGN

Use REST APIs.

Keep APIs:

- Simple.
- Predictable.
- Consistent.

Use correct HTTP status codes.

---

# 23. PRICING

The backend is always the source of truth for pricing.

Calculate:

service price + add-on prices

Do not trust frontend totals.

---

# 24. BOOKING SECURITY

The backend must prevent:

- Invalid bookings.
- Unauthorized bookings.
- Booking another customer's vehicle.
- Booking inactive services.
- Booking outside working hours.
- Double booking.

---

# 25. SEO

Public pages should include:

- Unique titles.
- Meta descriptions.
- Open Graph metadata.
- Semantic HTML.
- Canonical URLs where appropriate.
- Sitemap.
- Robots configuration.
- Structured data where appropriate.

Private pages must not be indexed.

---

# 26. PERFORMANCE

Prefer:

- Server Components.
- Optimized images.
- Minimal client JavaScript.
- Efficient API calls.
- Efficient database queries.

Do not optimize blindly.

---

# 27. ENVIRONMENT VARIABLES

Never hardcode secrets.

Use:

.env

and provide:

.env.example

Never commit real secrets.

---

# 28. GIT SAFETY

Before modifying existing code:

Inspect the repository.

Do not:

- Delete unrelated work.
- Rewrite unrelated modules.
- Remove dependencies without checking.
- Destroy configuration unnecessarily.

---

# 29. TESTING

Every task must be validated.

Where applicable run:

- TypeScript.
- ESLint.
- Unit tests.
- Integration tests.
- End-to-end tests.
- Production builds.

Never claim PASS if the command failed.

---

# 30. TASK REPORTING

After every task, produce a concise report:

## TASK X COMPLETED

Implemented:
- ...

Files changed:
- ...

Validation:
- TypeScript: PASS
- ESLint: PASS
- Tests: PASS
- Frontend Build: PASS
- Backend Build: PASS

Then immediately continue with the next task.

Do not wait for user approval unless a blocker exists.

---

# 31. DEFINITION OF DONE

A task is not complete if:

- Required functionality is missing.
- Critical tests fail.
- Production build fails.
- Critical TypeScript errors remain.
- Authentication is insecure.
- Authorization can be bypassed.
- Required validation is missing.

Fix problems before continuing.

---

# 32. PROJECT SCOPE

Keep the project medium-sized and interview-friendly.

Do NOT add:

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

unless explicitly required.

---

# 33. FINAL RULE

The agent must work like this:

READ
↓
UNDERSTAND
↓
IMPLEMENT TASK 1
↓
TEST
↓
FIX
↓
VALIDATE
↓
IMPLEMENT TASK 2
↓
TEST
↓
FIX
↓
VALIDATE
↓
IMPLEMENT TASK 3
↓
...
↓
TASK 6
↓
FINAL QA
↓
PROJECT COMPLETE

Do not stop between normal tasks.

Only stop for genuine blockers or required user decisions.

# END OF AGENTS.md
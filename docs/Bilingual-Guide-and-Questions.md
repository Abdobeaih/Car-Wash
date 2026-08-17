# Mobile CarCare — English and Arabic Guide

**Repository:** [Abdobeaih/Car-Wash](https://github.com/Abdobeaih/Car-Wash)

This document gives an English explanation followed by an Arabic explanation. The complete source code remains in the repository under `apps/web` and `apps/api`; the snippets below show the most important implementation patterns without duplicating every source file.

---

## Part A — English

### 1. What is the project?

Mobile CarCare is a full-stack mobile car-wash booking system. Customers browse services, select a vehicle and add-ons, choose a location and appointment time, and submit a booking. Administrators manage services, add-ons, customers, bookings, messages, notifications, and the calendar.

The frontend is Next.js, React, TypeScript, and Tailwind CSS. The backend is NestJS with MongoDB and Mongoose. The frontend is located in `apps/web`; the API is located in `apps/api`.

### 2. How data moves through the system

The browser calls `/api`. The Next.js catch-all route forwards that request to the NestJS API. The API validates the request, reads or writes MongoDB, and returns JSON. Authentication uses a JWT stored in browser localStorage under `mcc_token`.

```ts
// apps/web/src/lib/api.ts — simplified request pattern
const token = getToken();
const response = await fetch(`${API_URL}${path}`, {
  method,
  headers: {
    'Content-Type': 'application/json',
    ...(token ? { Authorization: `Bearer ${token}` } : {}),
  },
  body: body === undefined ? undefined : JSON.stringify(body),
});
```

In the browser, `API_URL` is `/api`; during server rendering it uses `API_URL`, `NEXT_PUBLIC_API_URL`, or `http://localhost:3001`. If the API is unreachable, the proxy returns HTTP 502.

### 3. Local installation

Use Node.js 20.9 or newer, npm, MongoDB, and Git.

```bash
npm install
cp .env.example .env
cp apps/api/.env.example apps/api/.env
cp apps/web/.env.example apps/web/.env.local
npm run dev:api   # http://localhost:3001
npm run dev:web   # http://localhost:3000
```

The most important variables are `DATABASE_URL`, `JWT_SECRET`, `JWT_EXPIRES_IN`, `PORT`, `CORS_ORIGINS`, and `NEXT_PUBLIC_API_URL`. In production, set `API_URL` in the web host so the `/api` proxy can reach the deployed API.

### 4. Authentication code

Registration creates customers only. Admin accounts cannot be created through the public registration endpoint. Passwords are hashed with bcrypt. Login returns a JWT containing the user identity and role.

```ts
// apps/api/src/auth/auth.service.ts — essential behavior
const existing = await this.usersService.findByEmail(dto.email);
if (existing) throw new BadRequestException('Account already exists.');
const user = await this.usersService.create({
  name: dto.name,
  email: dto.email,
  password: dto.password,
  role: UserRole.CUSTOMER,
});
return { user, token: this.signToken(user._id, user.email, user.role) };
```

Protected requests use `Authorization: Bearer <token>`. Admin controllers add `RolesGuard` and require `ADMIN`.

### 5. Booking process

The customer flow is service, vehicle, add-ons, location, date, time, review, and confirmation. The final request is sent to `POST /bookings`.

```ts
// apps/api/src/bookings/bookings.service.ts — authoritative pricing
const services = await this.serviceModel.find({ _id: { $in: serviceIds } });
const addOns = await this.addOnModel.find({ _id: { $in: addOnIds } });
const items = dto.services.map((selection) => {
  const service = serviceById.get(selection.serviceId)!;
  const addOnCost = selection.addOnIds.reduce(
    (sum, id) => sum + (addOnById.get(id)?.price ?? 0), 0,
  );
  return {
    serviceId: selection.serviceId,
    addOnIds: selection.addOnIds,
    duration: service.duration,
    subtotal: service.basePrice,
    total: service.basePrice + addOnCost,
  };
});
```

The API then checks that the vehicle belongs to the customer, verifies that services and add-ons are active, calculates the duration and end time, enforces working hours from `09:00` to `18:00`, and rejects overlap with a non-cancelled booking using HTTP 409. The stored status is `PENDING`, and payment status is `PENDING`.

```ts
// Overlap rule
$or: [
  { startTime: { $lt: endTime }, endTime: { $gt: dto.startTime } },
]
```

Customers can list, view, and cancel their own bookings. Completed bookings cannot be cancelled. New bookings and cancellations create admin notifications.

### 6. Main API routes

| Method | Route | Meaning |
| --- | --- | --- |
| `GET` | `/health` | Health check. |
| `POST` | `/auth/register` | Register a customer. |
| `POST` | `/auth/login` | Login and receive JWT. |
| `GET/PATCH` | `/auth/me` | Read or update the current profile. |
| `GET` | `/services`, `/services/:slug` | Browse active services. |
| `GET` | `/add-ons` | Browse active add-ons. |
| `GET` | `/availability` | Get available hourly slots. |
| `GET/POST` | `/bookings` | List or create customer bookings. |
| `GET` | `/bookings/:id` | Read one owned booking. |
| `POST` | `/bookings/:id/cancel` | Cancel one owned booking. |
| `GET/POST/PATCH/DELETE` | `/admin/services*`, `/admin/add-ons*` | Admin catalog management. |
| `GET/PATCH` | `/admin/bookings*` | Admin booking management. |
| `GET` | `/admin/dashboard`, `/admin/calendar`, `/admin/customers` | Admin reporting and operations. |
| `GET/PATCH` | `/notifications*` | Read and update notifications. |
| `POST` | `/contact` | Submit a contact message. |

### 7. Database models

`User` stores identity, email, password hash, role, and password-reset fields. `Vehicle` belongs to a user. `CarService` and `AddOn` are the catalog. `Booking` stores the customer, vehicle, service lines, add-ons, location, appointment interval, calculated prices, status, and payment status. `Notification` belongs to a recipient. `ContactMessage` stores public contact requests and a read flag.

### 8. Admin operations

Admin routes are protected twice: JWT authentication proves the identity, and the roles guard requires `ADMIN`. The dashboard calculates booking counts and revenue. Catalog screens provide create, edit, activate, deactivate, and delete operations. Booking status changes generate customer notifications. The calendar excludes cancelled bookings and populates customer, vehicle, and service details.

### 9. Known limitations

There is no payment gateway, technician assignment, email/SMS provider, or live tracking. Password-reset codes are returned in the response because email delivery is not configured. Logout clears the browser token but does not revoke the JWT server-side. Booking conflict protection is a read-then-write check and should be strengthened with a transaction or concurrency strategy for production.

The build passed and the API tests passed. The aggregate typecheck reports four binary-image import/type-declaration errors in the homepage, and the aggregate test command exits because the web workspace has no `test` script. These items should be fixed before a production release.

### 10. Where to find all code

The full implementation is available in the repository:

- Frontend pages: `apps/web/src/app`
- Frontend components: `apps/web/src/components`
- Frontend API and types: `apps/web/src/lib`
- Backend modules: `apps/api/src`
- Backend tests: `apps/api/test`
- Environment templates: `.env.example`, `apps/api/.env.example`, and `apps/web/.env.example`

---

## Part B — العربية

### ١. ما هو المشروع؟

تطبيق **Mobile CarCare** هو نظام متكامل لحجز خدمات غسيل وتنظيف السيارات المتنقلة. يستطيع العميل تصفح الخدمات، اختيار السيارة والإضافات، إدخال الموقع، اختيار التاريخ والوقت، ثم إرسال الحجز. يستطيع المدير إدارة الخدمات والإضافات والعملاء والحجوزات والرسائل والإشعارات والتقويم.

الواجهة الأمامية مبنية باستخدام Next.js وReact وTypeScript وTailwind CSS. أما الواجهة الخلفية فهي مبنية باستخدام NestJS وMongoDB وMongoose. توجد الواجهة الأمامية داخل `apps/web`، وتوجد الواجهة الخلفية داخل `apps/api`.

### ٢. كيف تنتقل البيانات داخل النظام؟

يرسل المتصفح الطلبات إلى المسار `/api`. يقوم مسار Next.js العام بتمرير الطلب إلى واجهة NestJS الخلفية. تتحقق الواجهة الخلفية من الطلب، ثم تقرأ أو تعدل MongoDB، وبعد ذلك تعيد بيانات JSON. تتم المصادقة باستخدام JWT ويُخزَّن الرمز في localStorage تحت المفتاح `mcc_token`.

```ts
// apps/web/src/lib/api.ts — النمط الأساسي لإرسال الطلب
const token = getToken();
const response = await fetch(`${API_URL}${path}`, {
  method,
  headers: {
    'Content-Type': 'application/json',
    ...(token ? { Authorization: `Bearer ${token}` } : {}),
  },
  body: body === undefined ? undefined : JSON.stringify(body),
});
```

في المتصفح تكون قيمة `API_URL` هي `/api`. أما أثناء العرض من الخادم فتُستخدم القيم `API_URL` أو `NEXT_PUBLIC_API_URL` أو العنوان المحلي `http://localhost:3001`. إذا تعذر الوصول إلى الواجهة الخلفية يعيد الوكيل الخطأ HTTP 502.

### ٣. تثبيت المشروع محلياً

يُنصح باستخدام Node.js بإصدار 20.9 أو أحدث، مع npm وMongoDB وGit.

```bash
npm install
cp .env.example .env
cp apps/api/.env.example apps/api/.env
cp apps/web/.env.example apps/web/.env.local
npm run dev:api   # الواجهة الخلفية على المنفذ 3001
npm run dev:web   # الواجهة الأمامية على المنفذ 3000
```

أهم المتغيرات هي `DATABASE_URL` و`JWT_SECRET` و`JWT_EXPIRES_IN` و`PORT` و`CORS_ORIGINS` و`NEXT_PUBLIC_API_URL`. في بيئة الإنتاج يجب ضبط `API_URL` في استضافة الواجهة الأمامية حتى يستطيع الوكيل `/api` الوصول إلى الواجهة الخلفية المنشورة.

### ٤. كود المصادقة

التسجيل العام ينشئ حسابات العملاء فقط، ولا يسمح بإنشاء حساب مدير من خلال التسجيل العام. تُشفَّر كلمات المرور باستخدام bcrypt. عند تسجيل الدخول يعيد النظام JWT يحتوي على هوية المستخدم ودوره.

```ts
// apps/api/src/auth/auth.service.ts — السلوك الأساسي
const existing = await this.usersService.findByEmail(dto.email);
if (existing) throw new BadRequestException('Account already exists.');
const user = await this.usersService.create({
  name: dto.name,
  email: dto.email,
  password: dto.password,
  role: UserRole.CUSTOMER,
});
return { user, token: this.signToken(user._id, user.email, user.role) };
```

تُرسل الطلبات المحمية في رأس HTTP بالشكل `Authorization: Bearer <token>`. وتضيف مسارات المدير `RolesGuard` وتشترط الدور `ADMIN`.

### ٥. عملية الحجز

يمر العميل بالمراحل التالية: الخدمة، السيارة، الإضافات، الموقع، التاريخ، الوقت، المراجعة، ثم التأكيد. يُرسل الطلب النهائي إلى `POST /bookings`.

```ts
// apps/api/src/bookings/bookings.service.ts — حساب السعر من الخادم
const services = await this.serviceModel.find({ _id: { $in: serviceIds } });
const addOns = await this.addOnModel.find({ _id: { $in: addOnIds } });
const items = dto.services.map((selection) => {
  const service = serviceById.get(selection.serviceId)!;
  const addOnCost = selection.addOnIds.reduce(
    (sum, id) => sum + (addOnById.get(id)?.price ?? 0), 0,
  );
  return {
    serviceId: selection.serviceId,
    addOnIds: selection.addOnIds,
    duration: service.duration,
    subtotal: service.basePrice,
    total: service.basePrice + addOnCost,
  };
});
```

يتحقق الخادم من ملكية السيارة للعميل، ومن وجود الخدمات والإضافات وأنها نشطة، ثم يحسب المدة ووقت النهاية، ويفرض ساعات العمل من `09:00` إلى `18:00`. كما يرفض أي تعارض مع حجز غير ملغى ويرجع HTTP 409. يُحفظ الحجز بالحالة `PENDING`، وتكون حالة الدفع `PENDING`.

```ts
// قاعدة اكتشاف التعارض
$or: [
  { startTime: { $lt: endTime }, endTime: { $gt: dto.startTime } },
]
```

يستطيع العميل عرض حجوزاته وفتح تفاصيلها وإلغاءها. لا يمكن إلغاء الحجز المكتمل. كما تُرسل إشعارات إلى المديرين عند إنشاء حجز أو إلغائه.

### ٦. أهم مسارات API

| الطريقة | المسار | الوظيفة |
| --- | --- | --- |
| `GET` | `/health` | فحص صحة الواجهة الخلفية. |
| `POST` | `/auth/register` | تسجيل عميل جديد. |
| `POST` | `/auth/login` | تسجيل الدخول وإصدار JWT. |
| `GET/PATCH` | `/auth/me` | قراءة أو تعديل الملف الشخصي الحالي. |
| `GET` | `/services` و`/services/:slug` | عرض الخدمات النشطة. |
| `GET` | `/add-ons` | عرض الإضافات النشطة. |
| `GET` | `/availability` | جلب الأوقات المتاحة. |
| `GET/POST` | `/bookings` | عرض أو إنشاء حجوزات العميل. |
| `GET` | `/bookings/:id` | عرض حجز يملكه العميل. |
| `POST` | `/bookings/:id/cancel` | إلغاء حجز يملكه العميل. |
| `GET/POST/PATCH/DELETE` | `/admin/services*` و`/admin/add-ons*` | إدارة الكتالوج للمدير. |
| `GET/PATCH` | `/admin/bookings*` | إدارة الحجوزات للمدير. |
| `GET` | `/admin/dashboard` و`/admin/calendar` و`/admin/customers` | تقارير وعمليات المدير. |
| `GET/PATCH` | `/notifications*` | قراءة وتحديث الإشعارات. |
| `POST` | `/contact` | إرسال رسالة تواصل. |

### ٧. نماذج قاعدة البيانات

يخزن نموذج `User` الهوية والبريد وكلمة المرور المشفرة والدور وبيانات استعادة كلمة المرور. ترتبط `Vehicle` بمستخدم. تمثل `CarService` و`AddOn` بيانات الكتالوج. يخزن `Booking` العميل والسيارة والخدمات والإضافات والموقع ووقت الموعد والأسعار المحسوبة والحالة وحالة الدفع. ترتبط `Notification` بالمستلم. ويخزن `ContactMessage` رسائل التواصل وحالة القراءة.

### ٨. وظائف المدير

تُحمى مسارات المدير بطبقتين: يتحقق JWT من هوية المستخدم، ثم يتأكد حارس الأدوار من أن دوره `ADMIN`. تحسب لوحة التحكم أعداد الحجوزات والإيرادات. وتوفر صفحات الكتالوج عمليات الإنشاء والتعديل والتفعيل والتعطيل والحذف. عند تغيير حالة الحجز تُرسل إشعارات إلى العميل. ويستبعد التقويم الحجوزات الملغاة ويجلب بيانات العميل والسيارة والخدمة.

### ٩. القيود الحالية

لا توجد بوابة دفع أو خدمة تعيين فني أو رسائل بريدية/نصية أو تتبع مباشر. يتم إرجاع رمز استعادة كلمة المرور في الاستجابة لعدم وجود خدمة بريد، ولذلك فالميزة مناسبة للتطوير وليست جاهزة للاستعمال الحقيقي. يؤدي تسجيل الخروج إلى حذف الرمز من المتصفح، لكنه لا يلغي JWT من الخادم. كما أن منع تعارض الحجوزات يعتمد على فحص ثم حفظ، ويحتاج إلى معاملة قاعدة بيانات أو استراتيجية تزامن أقوى في الإنتاج.

نجح بناء المشروع، كما نجحت اختبارات الواجهة الخلفية. لكن فحص الأنواع الكامل يبلغ عن أربع مشكلات استيراد/تعريف للصور في الصفحة الرئيسية، كما يفشل أمر الاختبارات العام لأن مساحة عمل الواجهة الأمامية لا تحتوي على سكربت `test`. يجب إصلاح هذه النقاط قبل إطلاق الإنتاج.

### ١٠. أين يوجد كامل الكود؟

الكود الكامل موجود في المستودع:

- صفحات الواجهة الأمامية: `apps/web/src/app`
- مكونات الواجهة الأمامية: `apps/web/src/components`
- طلبات API والأنواع: `apps/web/src/lib`
- وحدات الخادم: `apps/api/src`
- اختبارات الخادم: `apps/api/test`
- نماذج البيئة: `.env.example` و`apps/api/.env.example` و`apps/web/.env.example`

---

# Comprehensive Questions and Answers — أسئلة وأجوبة شاملة

| # | English question and answer | السؤال والإجابة بالعربية |
| --- | --- | --- |
| 1 | **What is the purpose of the application?** It books mobile car-care services at the customer’s location. | **ما هدف التطبيق؟** حجز خدمات العناية بالسيارات المتنقلة في موقع العميل. |
| 2 | **What are the two main applications?** `apps/web` is Next.js; `apps/api` is NestJS. | **ما التطبيقان الرئيسيان؟** `apps/web` للواجهة و`apps/api` للخادم. |
| 3 | **What database is used?** MongoDB through Mongoose. | **ما قاعدة البيانات؟** MongoDB من خلال Mongoose. |
| 4 | **What does the `/api` route do?** It proxies browser requests from Next.js to the API. | **ماذا يفعل `/api`؟** يمرر طلبات المتصفح من Next.js إلى الخادم. |
| 5 | **Where is the browser token stored?** In localStorage as `mcc_token`. | **أين يخزن رمز المتصفح؟** في localStorage باسم `mcc_token`. |
| 6 | **Can public registration create an admin?** No. Registration forces the customer role. | **هل يمكن إنشاء مدير بالتسجيل العام؟** لا، التسجيل العام ينشئ عميلاً فقط. |
| 7 | **How are passwords protected?** They are hashed and verified with bcrypt. | **كيف تحمى كلمات المرور؟** تشفر وتتحقق باستخدام bcrypt. |
| 8 | **How is an admin request protected?** JWT authentication plus the roles guard requiring `ADMIN`. | **كيف يحمى طلب المدير؟** JWT مع حارس الأدوار الذي يشترط `ADMIN`. |
| 9 | **Who calculates the final booking price?** The backend using MongoDB catalog data. | **من يحسب السعر النهائي؟** الخادم اعتماداً على بيانات الكتالوج في MongoDB. |
| 10 | **What happens if a service is inactive?** The API rejects the booking. | **ماذا يحدث إذا كانت الخدمة غير نشطة؟** يرفض الخادم الحجز. |
| 11 | **What are the working hours?** `09:00–18:00`. | **ما ساعات العمل؟** من `09:00` إلى `18:00`. |
| 12 | **How are conflicts detected?** Intervals overlap when the existing start is before the new end and the existing end is after the new start. | **كيف يكتشف التعارض؟** عندما يبدأ الحجز القديم قبل نهاية الجديد وينتهي بعد بداية الجديد. |
| 13 | **What status does a new booking receive?** `PENDING`. | **ما حالة الحجز الجديد؟** `PENDING`. |
| 14 | **Can a customer cancel a completed booking?** No. | **هل يستطيع العميل إلغاء حجز مكتمل؟** لا. |
| 15 | **What happens after an admin changes status?** The customer receives an in-app notification. | **ماذا يحدث بعد تغيير المدير للحالة؟** تصل إشعار داخل التطبيق إلى العميل. |
| 16 | **What does the dashboard calculate?** Booking counts, customer count, and non-cancelled revenue. | **ماذا تحسب لوحة التحكم؟** أعداد الحجوزات والعملاء وإيرادات الحجوزات غير الملغاة. |
| 17 | **What does `ValidationPipe` do?** It validates DTOs, transforms values, and rejects unknown fields. | **ماذا يفعل `ValidationPipe`؟** يتحقق من DTO ويحول القيم ويرفض الحقول غير المعروفة. |
| 18 | **Why is vehicle ownership checked?** To prevent one customer from booking another customer’s vehicle. | **لماذا يتم التحقق من ملكية السيارة؟** لمنع استخدام سيارة عميل آخر. |
| 19 | **What does `GET /availability` return?** Available time slots for a date and selected service duration. | **ماذا يعيد `GET /availability`؟** الأوقات المتاحة لتاريخ ومدة الخدمات المختارة. |
| 20 | **What is missing from production readiness?** Payment, email delivery, technician tracking, stronger concurrency protection, and clean aggregate quality checks. | **ما الذي ينقص الجاهزية للإنتاج؟** الدفع والبريد والتتبع وحماية التزامن وإصلاح فحوص الجودة العامة. |
| 21 | **Why does logout not revoke a JWT?** The current API is stateless; logout clears only the browser token. | **لماذا لا يلغي تسجيل الخروج JWT؟** لأن الخادم عديم الحالة؛ يتم حذف الرمز من المتصفح فقط. |
| 22 | **Why does password recovery return a token?** No email service is configured, so the development API returns it directly. | **لماذا يعيد استرجاع كلمة المرور الرمز؟** لعدم إعداد خدمة بريد، يعيده خادم التطوير مباشرة. |
| 23 | **What does HTTP 409 mean during booking?** The requested time conflicts with another active booking. | **ماذا يعني HTTP 409 أثناء الحجز؟** الوقت المطلوب يتعارض مع حجز نشط آخر. |
| 24 | **Which command starts the API?** `npm run dev:api`. | **ما أمر تشغيل الخادم؟** `npm run dev:api`. |
| 25 | **Which command starts the web app?** `npm run dev:web`. | **ما أمر تشغيل الواجهة؟** `npm run dev:web`. |
| 26 | **Which command builds the project?** `npm run build`. | **ما أمر بناء المشروع؟** `npm run build`. |
| 27 | **Why can build pass while typecheck fails?** Next.js build accepts the current compilation path, while the separate TypeScript check reports missing binary module declarations. | **كيف ينجح البناء ويفشل فحص الأنواع؟** لأن البناء ومسار فحص TypeScript المنفصل يتعاملان مع تعريفات الصور بشكل مختلف. |
| 28 | **Why does the root test command fail?** The web workspace has no `test` script, although API tests pass. | **لماذا يفشل أمر الاختبارات العام؟** لأن الواجهة لا تحتوي على سكربت `test` رغم نجاح اختبارات الخادم. |
| 29 | **What is the first security action before deployment?** Replace development secrets, seed credentials, and the default JWT secret. | **ما أول إجراء أمني قبل النشر؟** استبدال أسرار التطوير وبيانات seed ومفتاح JWT الافتراضي. |
| 30 | **Where should a developer begin reading the code?** Start with `apps/api/src/app.module.ts`, `bootstrap.ts`, `auth`, `bookings`, then `apps/web/src/lib/api.ts`, `auth-context.tsx`, and `app/book`. | **من أين يبدأ المطور قراءة الكود؟** يبدأ من `app.module.ts` و`bootstrap.ts` ثم `auth` و`bookings` وبعدها `api.ts` و`auth-context.tsx` و`app/book`. |

## References

[1]: https://github.com/Abdobeaih/Car-Wash "Abdobeaih/Car-Wash GitHub repository"

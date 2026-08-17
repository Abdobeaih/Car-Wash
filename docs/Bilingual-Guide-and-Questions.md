# Mobile CarCare — English / العربية Paired Guide

**Repository / المستودع:** [Abdobeaih/Car-Wash](https://github.com/Abdobeaih/Car-Wash)

Each English paragraph is followed immediately by its Arabic translation. The complete source code is available in the repository under `apps/web` and `apps/api`.

كل فقرة باللغة الإنجليزية يتبعها مباشرة ترجمتها باللغة العربية. الكود الكامل موجود في المستودع داخل المجلدين `apps/web` و`apps/api`.

## 1. Project purpose / هدف المشروع

**English:** Mobile CarCare is a full-stack application for booking mobile car-wash and detailing services at the customer’s location. Customers browse services, select a vehicle and add-ons, choose a location and time, then submit a booking. Administrators manage services, add-ons, customers, bookings, messages, notifications, and the calendar.

**العربية:** تطبيق Mobile CarCare هو تطبيق متكامل لحجز خدمات غسيل وتنظيف السيارات المتنقلة في موقع العميل. يتصفح العملاء الخدمات، ويختارون السيارة والإضافات والموقع والوقت، ثم يرسلون الحجز. يستطيع المديرون إدارة الخدمات والإضافات والعملاء والحجوزات والرسائل والإشعارات والتقويم.

**English:** The frontend uses Next.js, React, TypeScript, and Tailwind CSS. The backend uses NestJS, TypeScript, MongoDB, and Mongoose. The frontend is in `apps/web`, and the API is in `apps/api`.

**العربية:** تستخدم الواجهة الأمامية Next.js وReact وTypeScript وTailwind CSS. وتستخدم الواجهة الخلفية NestJS وTypeScript وMongoDB وMongoose. توجد الواجهة الأمامية في `apps/web`، وتوجد واجهة API في `apps/api`.

## 2. System flow / طريقة عمل النظام

**English:** The browser sends requests to `/api`. The Next.js catch-all route forwards them to the NestJS API. The API validates the request, reads or writes MongoDB, and returns JSON. JWT authentication is stored in browser localStorage under `mcc_token`.

**العربية:** يرسل المتصفح الطلبات إلى `/api`. يقوم المسار العام في Next.js بتمريرها إلى واجهة NestJS. يتحقق الخادم من الطلب، ويقرأ أو يكتب في MongoDB، ثم يعيد JSON. يتم تخزين مصادقة JWT في localStorage باسم `mcc_token`.

```ts
// apps/web/src/lib/api.ts
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

**English:** In the browser, `API_URL` is `/api`. During server rendering it uses `API_URL`, `NEXT_PUBLIC_API_URL`, or `http://localhost:3001`. If the API cannot be reached, the proxy returns HTTP 502.

**العربية:** داخل المتصفح تكون قيمة `API_URL` هي `/api`. وأثناء العرض من الخادم يستخدم التطبيق `API_URL` أو `NEXT_PUBLIC_API_URL` أو العنوان المحلي `http://localhost:3001`. إذا تعذر الوصول إلى الخادم يعيد الوكيل الخطأ HTTP 502.

## 3. Installation / التثبيت

**English:** Use Node.js 20.9 or newer, npm, MongoDB, and Git.

**العربية:** استخدم Node.js بإصدار 20.9 أو أحدث، بالإضافة إلى npm وMongoDB وGit.

```bash
npm install
cp .env.example .env
cp apps/api/.env.example apps/api/.env
cp apps/web/.env.example apps/web/.env.local
npm run dev:api   # API: http://localhost:3001
npm run dev:web   # Web: http://localhost:3000
```

**English:** The key variables are `DATABASE_URL`, `JWT_SECRET`, `JWT_EXPIRES_IN`, `PORT`, `CORS_ORIGINS`, and `NEXT_PUBLIC_API_URL`. In production, set `API_URL` in the web hosting environment so the `/api` proxy can reach the deployed API.

**العربية:** أهم المتغيرات هي `DATABASE_URL` و`JWT_SECRET` و`JWT_EXPIRES_IN` و`PORT` و`CORS_ORIGINS` و`NEXT_PUBLIC_API_URL`. في الإنتاج اضبط `API_URL` في بيئة استضافة الواجهة حتى يصل الوكيل `/api` إلى الخادم المنشور.

| Variable / المتغير | Purpose / الوظيفة |
| --- | --- |
| `DATABASE_URL` | MongoDB connection / اتصال MongoDB |
| `JWT_SECRET` | JWT signing secret / مفتاح توقيع JWT |
| `JWT_EXPIRES_IN` | Token lifetime / مدة الرمز |
| `PORT` | API port, normally 3001 / منفذ الخادم وغالباً 3001 |
| `CORS_ORIGINS` | Allowed production origins / النطاقات المسموحة |
| `NEXT_PUBLIC_API_URL` | Local API URL / عنوان API المحلي |
| `API_URL` | Production proxy target / هدف الوكيل في الإنتاج |

## 4. Authentication / المصادقة

**English:** Public registration creates customers only. Admin accounts cannot be created through the public registration endpoint. Passwords are hashed with bcrypt. Login returns a JWT containing the user identity and role.

**العربية:** التسجيل العام ينشئ حسابات العملاء فقط. لا يمكن إنشاء حساب مدير من خلال التسجيل العام. يتم تشفير كلمات المرور باستخدام bcrypt. يعيد تسجيل الدخول JWT يحتوي على هوية المستخدم ودوره.

```ts
// apps/api/src/auth/auth.service.ts
const user = await this.usersService.create({
  name: dto.name,
  email: dto.email,
  password: dto.password,
  role: UserRole.CUSTOMER,
});
return { user, token: this.signToken(user._id, user.email, user.role) };
```

**English:** Protected requests use `Authorization: Bearer <token>`. Admin controllers use JWT authentication and a roles guard requiring `ADMIN`.

**العربية:** تستخدم الطلبات المحمية الرأس `Authorization: Bearer <token>`. وتستخدم وحدات المدير مصادقة JWT وحارس الأدوار الذي يشترط `ADMIN`.

## 5. Booking process / عملية الحجز

**English:** The customer flow is service, vehicle, add-ons, location, date, time, review, and confirmation. The final request is sent to `POST /bookings`.

**العربية:** يمر العميل بالمراحل التالية: الخدمة، السيارة، الإضافات، الموقع، التاريخ، الوقت، المراجعة، ثم التأكيد. يُرسل الطلب النهائي إلى `POST /bookings`.

**English:** The backend checks vehicle ownership, service and add-on existence, active status, current prices, duration, working hours, and conflicts. It recalculates the final total instead of trusting the browser. Working hours are `09:00–18:00`. A new booking is saved with status `PENDING` and payment status `PENDING`.

**العربية:** يتحقق الخادم من ملكية السيارة، ووجود الخدمات والإضافات ونشاطها، والأسعار الحالية، والمدة، وساعات العمل، والتعارضات. يعيد حساب السعر النهائي ولا يثق بحساب المتصفح. ساعات العمل من `09:00` إلى `18:00`. يحفظ الحجز الجديد بالحالة `PENDING` وحالة الدفع `PENDING`.

```ts
// apps/api/src/bookings/bookings.service.ts
const addOnCost = selection.addOnIds.reduce(
  (sum, id) => sum + (addOnById.get(id)?.price ?? 0),
  0,
);
const total = service.basePrice + addOnCost;
```

**English:** A conflict exists when the previous booking starts before the requested end and ends after the requested start. The API returns HTTP 409 for a conflict. Cancelled bookings do not block the interval.

**العربية:** يحدث التعارض عندما يبدأ الحجز السابق قبل نهاية الوقت المطلوب وينتهي بعد بداية الوقت المطلوب. يعيد الخادم HTTP 409 عند وجود تعارض. أما الحجوزات الملغاة فلا تمنع الفترة الزمنية.

```ts
$or: [
  { startTime: { $lt: endTime }, endTime: { $gt: dto.startTime } },
]
```

## 6. Customer and admin features / وظائف العميل والمدير

**English:** Customers can register, log in, manage their own vehicles, create and view bookings, cancel eligible bookings, update their profile, change their password, and read notifications.

**العربية:** يستطيع العملاء التسجيل وتسجيل الدخول وإدارة سياراتهم وإنشاء الحجوزات وعرضها وإلغاء الحجوزات المسموح بها وتعديل الملف الشخصي وتغيير كلمة المرور وقراءة الإشعارات.

**English:** Administrators can view dashboard metrics, manage services and add-ons, search customers and bookings, update booking status, view the calendar, read contact messages, and manage notifications.

**العربية:** يستطيع المديرون عرض إحصاءات لوحة التحكم، وإدارة الخدمات والإضافات، والبحث عن العملاء والحجوزات، وتغيير حالة الحجز، وعرض التقويم، وقراءة رسائل التواصل، وإدارة الإشعارات.

## 7. API routes / مسارات API

| Method / الطريقة | Route / المسار | English purpose / الوظيفة بالإنجليزية | الوظيفة بالعربية |
| --- | --- | --- | --- |
| `GET` | `/health` | Health check | فحص صحة الخادم |
| `POST` | `/auth/register` | Register customer | تسجيل عميل |
| `POST` | `/auth/login` | Login and issue JWT | تسجيل الدخول وإصدار JWT |
| `GET/PATCH` | `/auth/me` | Read/update current profile | قراءة/تعديل الملف الشخصي |
| `GET` | `/services`, `/services/:slug` | Browse services | عرض الخدمات |
| `GET` | `/add-ons` | Browse add-ons | عرض الإضافات |
| `GET` | `/availability` | Get available slots | جلب الأوقات المتاحة |
| `GET/POST` | `/bookings` | List/create bookings | عرض/إنشاء الحجوزات |
| `GET` | `/bookings/:id` | View owned booking | عرض حجز مملوك |
| `POST` | `/bookings/:id/cancel` | Cancel booking | إلغاء حجز |
| `GET/POST/PATCH/DELETE` | `/admin/services*` | Manage services | إدارة الخدمات |
| `GET/POST/PATCH/DELETE` | `/admin/add-ons*` | Manage add-ons | إدارة الإضافات |
| `GET/PATCH` | `/admin/bookings*` | Manage bookings | إدارة الحجوزات |
| `GET` | `/admin/dashboard` | Dashboard metrics | إحصاءات لوحة التحكم |
| `GET` | `/admin/calendar` | Calendar data | بيانات التقويم |
| `GET` | `/admin/customers` | Customer list | قائمة العملاء |
| `GET/PATCH` | `/notifications*` | Read/update notifications | قراءة/تحديث الإشعارات |
| `POST` | `/contact` | Submit contact message | إرسال رسالة تواصل |

## 8. Database models / نماذج قاعدة البيانات

**English:** `User` stores identity, email, password hash, role, and reset-password fields. `Vehicle` belongs to a user. `CarService` and `AddOn` are catalog records. `Booking` stores the customer, vehicle, services, add-ons, location, time interval, calculated prices, status, and payment status. `Notification` belongs to a recipient. `ContactMessage` stores public messages and read state.

**العربية:** يخزن نموذج `User` الهوية والبريد وكلمة المرور المشفرة والدور وبيانات الاستعادة. يرتبط نموذج `Vehicle` بمستخدم. يمثل `CarService` و`AddOn` بيانات الكتالوج. يخزن `Booking` العميل والسيارة والخدمات والإضافات والموقع والفترة الزمنية والأسعار المحسوبة والحالة وحالة الدفع. ترتبط `Notification` بالمستلم. ويخزن `ContactMessage` الرسائل العامة وحالة القراءة.

## 9. Code locations / أماكن الكود

| English | العربية |
| --- | --- |
| Frontend pages: `apps/web/src/app` | صفحات الواجهة: `apps/web/src/app` |
| Frontend components: `apps/web/src/components` | مكونات الواجهة: `apps/web/src/components` |
| API client and types: `apps/web/src/lib` | عميل API والأنواع: `apps/web/src/lib` |
| Backend modules: `apps/api/src` | وحدات الخادم: `apps/api/src` |
| Backend tests: `apps/api/test` | اختبارات الخادم: `apps/api/test` |
| Environment templates: `.env.example` and workspace examples | نماذج البيئة: `.env.example` والنماذج داخل التطبيقات |

**English:** The repository contains all source code. The snippets in this document are the important patterns; use the GitHub source tree when you need the complete implementation of a specific file.

**العربية:** يحتوي المستودع على الكود المصدري الكامل. المقاطع في هذا المستند توضح الأنماط المهمة، ويمكنك فتح شجرة المصدر في GitHub عند الحاجة إلى الملف الكامل.

## 10. Limitations and verification / القيود والتحقق

**English:** There is no payment gateway, email/SMS provider, technician assignment, or live tracking. Password-reset codes are returned directly because email delivery is not configured. Logout clears the browser token but does not revoke the JWT on the server. Booking conflict protection should use stronger concurrency protection before high-volume production use.

**العربية:** لا توجد بوابة دفع أو خدمة بريد/رسائل نصية أو تعيين فني أو تتبع مباشر. يتم إرجاع رموز استعادة كلمة المرور مباشرة لعدم إعداد البريد. يحذف تسجيل الخروج الرمز من المتصفح لكنه لا يلغي JWT على الخادم. ويجب تقوية حماية تعارض الحجوزات قبل الاستخدام الإنتاجي واسع النطاق.

**English:** The production build passed and API tests passed. Aggregate type checking reports four homepage binary-image import/type errors, and the aggregate test command stops because the web workspace has no `test` script. These issues should be fixed before production deployment.

**العربية:** نجح بناء المشروع ونجحت اختبارات API. لكن فحص الأنواع العام يسجل أربع مشكلات متعلقة باستيراد الصور في الصفحة الرئيسية، ويتوقف أمر الاختبارات العام لأن مساحة عمل الواجهة لا تحتوي على سكربت `test`. يجب إصلاح هذه المشكلات قبل النشر في الإنتاج.

# Questions and answers / الأسئلة والأجوبة

### 1. What is the purpose of the app?

**Answer:** It books mobile car-care services at the customer’s location.

**بالعربية — ما هدف التطبيق؟** حجز خدمات العناية بالسيارات المتنقلة في موقع العميل.

### 2. What are the two main applications?

**Answer:** `apps/web` is the Next.js frontend and `apps/api` is the NestJS backend.

**بالعربية — ما التطبيقان الرئيسيان؟** `apps/web` هي الواجهة المبنية بـ Next.js و`apps/api` هو الخادم المبني بـ NestJS.

### 3. Which database is used?

**Answer:** MongoDB through Mongoose.

**بالعربية — ما قاعدة البيانات المستخدمة؟** MongoDB من خلال Mongoose.

### 4. Where is the JWT stored in the browser?

**Answer:** In localStorage under `mcc_token`.

**بالعربية — أين يخزن JWT؟** في localStorage تحت الاسم `mcc_token`.

### 5. Can public registration create an admin account?

**Answer:** No. Public registration creates customers only.

**بالعربية — هل ينشئ التسجيل العام مديراً؟** لا، ينشئ عملاء فقط.

### 6. Who calculates the final booking price?

**Answer:** The backend using current MongoDB service and add-on prices.

**بالعربية — من يحسب السعر النهائي؟** الخادم باستخدام الأسعار الحالية في MongoDB.

### 7. What are the working hours?

**Answer:** 09:00 to 18:00.

**بالعربية — ما ساعات العمل؟** من الساعة 09:00 إلى 18:00.

### 8. What happens when a time conflict exists?

**Answer:** The API rejects the booking with HTTP 409.

**بالعربية — ماذا يحدث عند وجود تعارض زمني؟** يرفض الخادم الحجز ويرجع HTTP 409.

### 9. What is the initial booking status?

**Answer:** `PENDING`; payment status is also `PENDING`.

**بالعربية — ما حالة الحجز الأولية؟** `PENDING`، وحالة الدفع أيضاً `PENDING`.

### 10. Can a customer cancel a completed booking?

**Answer:** No.

**بالعربية — هل يمكن إلغاء حجز مكتمل؟** لا.

### 11. How are admin routes protected?

**Answer:** JWT authentication plus a roles guard requiring `ADMIN`.

**بالعربية — كيف تحمى مسارات المدير؟** بواسطة JWT وحارس أدوار يشترط `ADMIN`.

### 12. What does `ValidationPipe` do?

**Answer:** It validates DTOs, transforms values, and rejects unknown properties.

**بالعربية — ماذا يفعل `ValidationPipe`؟** يتحقق من DTO ويحول القيم ويرفض الخصائص غير المعروفة.

### 13. Why is vehicle ownership checked?

**Answer:** To prevent a customer from booking another customer’s vehicle.

**بالعربية — لماذا يتم التحقق من ملكية السيارة؟** لمنع العميل من حجز سيارة عميل آخر.

### 14. What does `GET /availability` return?

**Answer:** Available appointment slots for a date and selected service duration.

**بالعربية — ماذا يعيد `GET /availability`؟** الأوقات المتاحة لتاريخ ومدة الخدمات المختارة.

### 15. What is still missing for production?

**Answer:** Payment, email delivery, technician tracking, stronger concurrency protection, and clean aggregate quality checks.

**بالعربية — ما الذي ينقص الإنتاج؟** الدفع والبريد وتتبع الفني وحماية التزامن وإصلاح فحوص الجودة العامة.

### 16. Which command starts the API?

**Answer:** `npm run dev:api`.

**بالعربية — ما أمر تشغيل API؟** `npm run dev:api`.

### 17. Which command starts the web application?

**Answer:** `npm run dev:web`.

**بالعربية — ما أمر تشغيل الواجهة؟** `npm run dev:web`.

### 18. Which command builds the project?

**Answer:** `npm run build`.

**بالعربية — ما أمر بناء المشروع؟** `npm run build`.

### 19. Why can build pass while typecheck fails?

**Answer:** The separate TypeScript check reports binary-image module/type declarations that the Next.js build currently handles differently.

**بالعربية — كيف ينجح البناء ويفشل فحص الأنواع؟** لأن فحص TypeScript المنفصل يسجل تعريفات مفقودة لوحدات الصور، بينما يتعامل بناء Next.js معها بطريقة مختلفة.

### 20. Where should a developer start reading the code?

**Answer:** Start with `apps/api/src/app.module.ts`, `bootstrap.ts`, `auth`, and `bookings`, then read `apps/web/src/lib/api.ts`, `auth-context.tsx`, and `app/book`.

**بالعربية — من أين يبدأ المطور قراءة الكود؟** يبدأ من `app.module.ts` و`bootstrap.ts` و`auth` و`bookings`، ثم يقرأ `api.ts` و`auth-context.tsx` و`app/book`.

## References / المراجع

[1]: https://github.com/Abdobeaih/Car-Wash "Abdobeaih/Car-Wash GitHub repository"

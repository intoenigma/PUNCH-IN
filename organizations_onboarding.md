Samajh gaya. Ab hum **sirf ORG flow** lock kar rahe hain (Super Admin UI PunchDesk me nahi).
Flow ye hoga:

1. **Admin Console (alag software)** → API call karke **PunchDesk me org create** karega
2. Org create hote hi **Org Owner user** create hoga
3. Org Owner **/login** se login karega
4. **First login** par onboarding wizard chalega (address, working hours, shifts, rules, etc.)
5. Onboarding complete hone ke baad org normal mode me chalega (users/devices/attendance/payroll etc.)

Neeche main tumhe **React UI pages + routes + APIs (Flask)** sab detail me de raha hoon.

---

# 1) ORG Creation via External Software (Admin Console → PunchDesk)

## 1.1 API Security (Must)

Ye endpoint **public users ke liye nahi hoga**.

**Auth options (Phase-1 simple):**

* `X-SERVICE-KEY: <secret>` (server-to-server)

**Recommended:**

* Service JWT + IP allowlist (Phase-2)

---

## 1.2 Internal API: Create Organization + Owner User

### Endpoint

`POST /internal/orgs`

### Headers

* `X-SERVICE-KEY: <SERVICE_SECRET>`

### Request Body

```json
{
  "org": {
    "name": "ABC Hospital",
    "org_type": "hospital",
    "email": "admin@abchospital.com",
    "mobile": "9876543210",
    "timezone": "Asia/Kolkata",
    "created_source": "admin_console",
    "created_by": "admin@yourconsole.com"
  },
  "owner": {
    "name": "Dr Sharma",
    "email": "owner@abchospital.com",
    "mobile": "9000000000"
  }
}
```

### Server Action

* `organizations` insert
* `users` insert (role = `ORG_OWNER`, org_id = new org id)
* password auto-generate (random)
* return success + temp password (Admin Console will email it)

### Response

```json
{
  "success": true,
  "data": {
    "org_id": "uuid-org",
    "owner_user_id": "uuid-user",
    "temp_password": "Abc@12345"
  }
}
```

### Validations

* org.email unique (global)
* owner.email unique per org (or global if single login system)
* org_type allowed list

---

# 2) PunchDesk Side: Org Login + First Time Onboarding

## 2.1 Public Login API (Org users)

### Endpoint

`POST /api/auth/login`

### Request

```json
{
  "email": "owner@abchospital.com",
  "password": "Abc@12345"
}
```

### Response

```json
{
  "success": true,
  "data": {
    "token": "jwt...",
    "user": {
      "id": "uuid-user",
      "org_id": "uuid-org",
      "role": "ORG_OWNER",
      "name": "Dr Sharma"
    },
    "org": {
      "id": "uuid-org",
      "name": "ABC Hospital",
      "org_type": "hospital"
    },
    "onboarding": {
      "is_completed": false,
      "next_step": "company_profile"
    }
  }
}
```

> `onboarding.is_completed` false hoga to UI direct onboarding wizard kholegi.

---

## 2.2 How to Track “First Login / Onboarding Completed”

### Option A (Recommended)

`organizations` me column:

* `onboarding_status` = `pending | in_progress | completed`
* `onboarding_completed_at` timestamp

### Minimal Tables Involved

* `organizations` (onboarding status)
* `organization_profiles` (address etc.)
* `organization_working_hours` (default rules)
* `shifts` (optional during onboarding)

---

# 3) Org Onboarding Wizard (React UI + API Design)

## 3.1 React Routes (Org Side)

### Auth pages

* `/login` → Login page

### Onboarding pages (Wizard)

* `/onboarding` (container)

  * `/onboarding/company-profile`
  * `/onboarding/working-hours`
  * `/onboarding/shifts`
  * `/onboarding/review`

### After onboarding

* `/dashboard` → Org dashboard

---

## 3.2 Onboarding Wizard UX Rules

* Wizard **step-by-step** (Back/Next)
* Each step **auto-save** (optional) OR explicit Save
* User cannot open `/dashboard` until onboarding completed
* Progress bar (Step 1/4 etc.)

---

# 4) Pages + Wireframe + Buttons + API per Page

## Page 1: Login

### Route

`/login`

### UI

**Fields**

* Email
* Password

**Buttons**

* Login

**Error states**

* Invalid creds
* Org inactive/suspended (show message)

**API**

* `POST /api/auth/login`

---

## Page 2: Onboarding Step 1 — Company Profile

### Route

`/onboarding/company-profile`

### UI Fields

* Organization Name (readonly or editable)
* Org Type (readonly)
* Address Line 1
* Address Line 2
* City
* State
* Pincode
* Contact Email
* Contact Mobile

### Buttons

* `Save & Next`
* `Save Draft` (optional)

### Wireframe (text)

```
[Onboarding - Step 1/4] Company Profile
--------------------------------------
Org Name: [ABC Hospital] (readonly)
Org Type: [Hospital]     (readonly)

Address Line 1: [__________]
Address Line 2: [__________]
City: [____]  State: [____]  Pincode: [____]

Contact Email: [__________]
Contact Mobile:[__________]

[Save Draft]              [Save & Next]
```

### API

#### GET (prefill)

`GET /api/org/onboarding/company-profile`

#### PUT (save)

`PUT /api/org/onboarding/company-profile`

**Request**

```json
{
  "address_line1": "...",
  "address_line2": "...",
  "city": "...",
  "state": "...",
  "pincode": "...",
  "contact_email": "...",
  "contact_mobile": "..."
}
```

**Response**

```json
{
  "success": true,
  "data": { "saved": true, "next_step": "working_hours" }
}
```

Backend handling:

* Upsert into `organization_profiles`
* Mark org onboarding_status = `in_progress`

---

## Page 3: Onboarding Step 2 — Working Hours & Rules

### Route

`/onboarding/working-hours`

### UI Fields

* Working Days (checkbox group: Mon..Sun)
* Default Work Start Time
* Default Work End Time
* Grace Minutes
* Overtime Allowed (toggle)

Optional (Phase 1):

* Half Day After time

### Buttons

* Back
* Save & Next

### Wireframe

```
[Onboarding - Step 2/4] Working Hours
--------------------------------------
Working Days: [Mon] [Tue] [Wed] [Thu] [Fri] [Sat] [Sun]

Start Time: [09:00]
End Time:   [18:00]

Grace Minutes: [10]
Overtime Allowed: [ ON/OFF ]

[Back]                     [Save & Next]
```

### API

`GET /api/org/onboarding/working-hours`
`PUT /api/org/onboarding/working-hours`

**Request**

```json
{
  "working_days": ["mon","tue","wed","thu","fri"],
  "work_start_time": "09:00",
  "work_end_time": "18:00",
  "grace_minutes": 10,
  "overtime_allowed": true
}
```

Backend:

* Upsert into `organization_working_hours`

---

## Page 4: Onboarding Step 3 — Shifts Setup

### Route

`/onboarding/shifts`

### UX goal

* Org can start with **one default shift**
* Additional shifts optional (Add Shift button)

### UI

Table/List of shifts:

* Shift Name
* Start
* End
* Grace
* Overnight toggle
* Default checkbox

Buttons:

* ➕ Add Shift
* Edit (pencil)
* Delete (trash)
* Back
* Save & Next

### Wireframe

```
[Onboarding - Step 3/4] Shifts
--------------------------------------
Shifts:
#  Name       Start  End   Grace  Overnight  Default  Actions
1  General    09:00  18:00  10     No         Yes      [Edit] [Del]
2  Night      20:00  05:00  5      Yes        No       [Edit] [Del]

[+ Add Shift]

[Back]                               [Save & Next]
```

### APIs

* `GET /api/org/shifts`
* `POST /api/org/shifts`
* `PUT /api/org/shifts/{id}`
* `DELETE /api/org/shifts/{id}` (soft delete recommended)

**Shift Create Request**

```json
{
  "shift_name": "General",
  "start_time": "09:00",
  "end_time": "18:00",
  "grace_minutes": 10,
  "overnight": false,
  "is_default": true
}
```

Backend rules:

* Only 1 default shift allowed → if new shift is_default=true, previous default set false.

---

## Page 5: Onboarding Step 4 — Review & Finish

### Route

`/onboarding/review`

### UI

* Summary cards:

  * Company Profile
  * Working Hours
  * Shifts count
* “Finish Setup” button

### Buttons

* Back
* ✅ Finish Setup

### Wireframe

```
[Onboarding - Step 4/4] Review
--------------------------------------
Company Profile: ✅ Completed
Working Hours:   ✅ Completed
Shifts:          ✅ 2 shifts configured

[Back]                    [Finish Setup]
```

### API

`POST /api/org/onboarding/complete`

Backend:

* Validate required data exists:

  * org profile present
  * working hours present
  * at least 1 shift exists (or auto create default)
* Update `organizations.onboarding_status = completed`
* Set `onboarding_completed_at = now()`

Response:

```json
{
  "success": true,
  "data": { "onboarding_completed": true }
}
```

---

# 5) After Onboarding: Org-only Experience (What happens next)

Once onboarding completed:

* User lands on `/dashboard`
* Now org can start:

  * create staff/users
  * register devices
  * receive attendance pushes

## Org Dashboard (basic)

### Route

`/dashboard`

Widgets (Phase 1):

* Present Today
* Absent Today
* Late Today
* On Leave Today

Quick Actions:

* Add User
* Register Device
* View Attendance

---

# 6) React Pages List (Org Side) — Final

## Public

* `/login` – login form

## Onboarding (guarded)

* `/onboarding/company-profile`
* `/onboarding/working-hours`
* `/onboarding/shifts`
* `/onboarding/review`

## App (after onboarding)

* `/dashboard`

> Next pages (later phase) would be `/users`, `/devices`, etc.
> Abhi tumne bola “sirf org explain karo”, so I’m stopping at org onboarding + dashboard.

---

# 7) Implementation Notes (Flask API Handling)

## Guard middleware rules

* If token valid BUT org onboarding_status != completed:

  * allow only `/api/org/onboarding/*` endpoints
  * deny other org endpoints

## Response standard

Always:

```json
{ "success": true|false, "message": "", "data": {} }
```
A responsive and secure Admin Panel built using **React**, designed for CleanPro’s internal management system.
This frontend interacts with a Node.js + Express backend and uses JWT for secure access.

---

🔵 SIGNUP FLOW (User Registration)

User fills signup form

Frontend validates:

Gmail-only email

10-digit phone

Strong password rules

Confirm password matches

Frontend sends:

POST /api/auth/signup

with FormData

Backend route receives request:

authRoutes → signupUser()

Backend validates again:

Required fields

Email format

Check if email exists

Backend hashes password (bcrypt)

User inserted into users table

Backend responds Signup successful

Frontend redirects to login page

🔵 LOGIN FLOW

User enters email, password, and role

Frontend sends:

POST /api/auth/login

Backend checks:

Email exists

Password matches

Role matches

Backend generates JWT (7 days)

Frontend stores token in localStorage

Logged-in UI displayed based on role

🔵 PROTECTED ROUTES FLOW (Role Based Access Control)

Each protected route uses:

authMiddleware → verifies JWT
allowRoles() → checks role

Examples:

Dashboard → admin only

Manage Users → admin only

Manage Cleaners → admin only

Manage Partners → admin only

Backend rejects unauthorized requests with:

403 Access Denied

🔵 FETCHING DASHBOARD COUNTS

Frontend loads:

GET /api/dashboard

Backend queries users table:

Total users

Admin count

Partner count

Cleaner count

User count

Data returned for dashboard UI.

🔵 DISPLAYING TABLES (Manage Users / Cleaners / Partners)

These pages all use the SAME data source:

GET /api/users

Then frontend filters:

role === "admin"

role === "partner"

role === "cleaner"

role === "user"

No separate tables needed.

🔵 UPDATE USER FLOW (Admin Only)

Admin clicks edit

Frontend sends:

PUT /api/users

Backend:

Validates email

Hashes password if changed

Updates user row in DB

🔵 DELETE USER FLOW (Admin Only)

Admin clicks delete

Frontend sends:

DELETE /api/users

Backend removes user from DB






                           ┌──────────────────────────┐
                           │        FRONTEND          │
                           │  React + Axios + JWT     │
                           └────────────┬─────────────┘
                                        │
                                        │ (User submits signup/login)
                                        ▼
                     ┌────────────────────────────────────────┐
                     │        /api/auth/signup (POST)          │
                     │        /api/auth/login  (POST)          │
                     └───────────────────────┬────────────────┘
                                             │
                                             ▼
                               ┌──────────────────────────┐
                               │     AUTH ROUTES          │
                               │  authRoutes.js           │
                               └────────────┬─────────────┘
                                             │
                                             ▼
                               ┌──────────────────────────┐
                               │   AUTH CONTROLLER        │
                               │   signupUser / loginUser │
                               └────────────┬─────────────┘
                                             │
                                             │
         ┌───────────────────────────────────┼───────────────────────────────────┐
         │                                   │                                   │
         ▼                                   ▼                                   ▼
┌────────────────┐                  ┌─────────────────────┐               ┌────────────────┐
│ FRONTEND VALID │                  │ BACKEND VALIDATION  │               │ PASSWORD HASH   │
│ Email / Phone  │                  │ Email exists?       │               │ bcrypt.hash     │
│ Password rules │                  │ Required fields?    │               └────────────────┘
└────────────────┘                  │ Normalize email     │
                                    └─────────────────────┘
                                             │
                                             ▼
                             ┌────────────────────────────────┐
                             │   DATABASE INSERT / SELECT     │
                             │     users table ONLY           │
                             └────────────────────────────────┘
                                             │
                                             ▼
                                 ┌────────────────────────┐
                                 │ AUTH SUCCESS RESPONSE  │
                                 │ Signup or Login OK     │
                                 └─────────┬──────────────┘
                                           │
                                           ▼
                           ┌────────────────────────────────┐
                           │   JWT TOKEN GENERATED          │
                           │   Stored in localStorage       │
                           └─────────────┬──────────────────┘
                                         │
                                         ▼
                           ┌───────────────────────────────────┐
                           │  PROTECTED ROUTES VIA JWT         │
                           │  /api/dashboard                   │
                           │  /api/users                       │
                           └─────────────┬─────────────────────┘
                                         │
                                         ▼
                           ┌───────────────────────────────────┐
                           │    AUTH MIDDLEWARE (Backend)      │
                           │  • Verifies JWT                   │
                           │  • Sets req.user                  │
                           └─────────────┬─────────────────────┘
                                         │
                                         ▼
                          ┌────────────────────────────────────┐
                          │    ROLE MIDDLEWARE (allowRoles)    │
                          │  Example: allowRoles("admin")      │
                          │  Deny if role mismatch             │
                          └─────────────┬─────────────────────┘
                                         │
                                         ▼
                      ┌────────────────────────────────────────────┐
                      │          USER CONTROLLER (Admin)           │
                      │      /api/users → list/update/delete       │
                      │      /api/dashboard → stats                │
                      └────────────────────────────────────────────┘
                                         │
                                         ▼
                           ┌───────────────────────────────────┐
                           │            FRONTEND UI            │
                           │ Dashboard, Manage Users, etc.     │
                           └───────────────────────────────────┘

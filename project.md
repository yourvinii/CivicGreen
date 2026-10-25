# PROJECT: CivicGreen --- Micro-Civic Ledger & Green Credit System

## 0. Mission and mandatory working rules

Build a working, demo-ready civic sustainability and green-credit
prototype from scratch during the Hack Day.

**Time budget:** approximately 3 hours. Prioritize one complete,
reliable end-to-end flow over extra features.

### Mandatory workflow for Antigravity

1.  Read this entire `PROJECT.md` before changing any files.
2.  Inspect the current workspace and report the proposed changes first.
3.  Build only one chunk at a time, in the order below.
4.  Before each chunk, briefly explain the files and features you intend
    to change, then wait for my explicit confirmation.
5.  After completing a chunk, stop. Explain what changed, how to run it,
    how to test it, and which environment variables are needed.
6.  Never start the next chunk until I say to continue.
7.  Use JavaScript, not TypeScript. Keep code readable, dependencies
    minimal, and comments short.
8.  Do not deploy, configure Docker, add CI/CD, or install unnecessary
    packages unless I ask.
9.  Never claim a feature works unless it has actually been implemented
    and tested.
10. Create project code during this Hack Day. Do not copy an existing
    complete project.
11. Use fictional demo data only. Do not collect real household,
    financial, identity, or other sensitive personal information.
12. Never claim that rewards are real, discounts are redeemable, or a
    municipality has approved an action unless a real integration
    exists.
13. Do not generate the entire application at once. Follow the chunk
    workflow strictly.
14. If time is short, reduce scope rather than skipping validation or
    pretending a feature works.

------------------------------------------------------------------------

## 1. Product idea

Citizens take small actions that help their neighbourhood and the
environment, but these actions are often not recognized or tracked.
CivicGreen demonstrates how a local civic platform could encourage such
behaviour through a transparent green-credit ledger.

Example activities: - Segregating household waste. - Using public
transportation. - Participating in community cleanliness drives. -
Joining environmental-awareness activities. - Supporting green-certified
local businesses. - Other approved civic or sustainability activities.

Citizens can submit an activity, see its review status, and view points
awarded after approval. A demo administrator can approve or reject
submissions. A ledger records point credits so the balance can be
explained and audited.

The application may show illustrative rewards such as a local-business
discount or a simulated utility rebate, but these are **examples only**.
The MVP must not process money, issue actual discounts, or connect to a
government department.

### Core demo flow

1.  Citizen opens the application.
2.  Citizen selects a civic activity and enters a short description and
    locality.
3.  The app creates an activity with `PENDING` status and zero points.
4.  Demo admin reviews the activity.
5.  Admin approves or rejects it.
6.  On approval, the backend calculates points using server-side rules
    and creates one ledger credit.
7.  Citizen refreshes the dashboard and sees the updated balance and
    ledger.
8.  Citizen can view a rewards catalogue clearly labelled as a
    simulation.

**Critical rule:** Submitting an activity must not award points. Only
approval can award points, and an activity must never be credited twice.

------------------------------------------------------------------------

## 2. Fixed technology stack

-   Frontend: React + Vite + React Router.
-   Styling: plain CSS initially. Use Tailwind only if already
    configured or it can be added quickly and reliably.
-   Backend: Node.js + Express.
-   Database: MongoDB + Mongoose.
-   HTTP: built-in `fetch`.
-   Language: JavaScript, not TypeScript.
-   Layout: `/client` and `/server`.
-   Environment variables: `.env` files; never hardcode secrets.

MongoDB is required for persistence. If `MONGO_URI` is absent or the
database is unavailable, report the setup issue clearly. Do not silently
switch to in-memory storage or claim persistence works.

Use an optional Gemini feature only in the optional chunk, and only
through the backend. The core app must not depend on AI.

------------------------------------------------------------------------

## 3. Out of scope

Do not build these in the MVP: - Real municipal or government API
integration. - Real property-tax or electricity-bill rebates. - Real
money, payments, financial transactions, or a
cryptocurrency/blockchain. - Actual reward redemption or settlement. -
Aadhaar or real identity verification. - Real SMS, email, or WhatsApp
delivery. - Complex authentication or permission systems. - Live maps,
GPS tracking, or GIS integration. - Photo/video uploads. -
Machine-learning model training. - WebSockets, microservices, Docker,
CI/CD, or deployment configuration. - Production-grade fraud
detection. - Multiple real municipality or business integrations.

Use a simple demo admin password if needed. Clearly label the project as
a hackathon prototype.

------------------------------------------------------------------------

## 4. Suggested folder structure

Keep the structure smaller if time is tight. Do not create empty files
just to match this tree.

``` text
civicgreen/
├── PROJECT.md
├── CONTRACT.md
├── .gitignore
├── server/
│   ├── package.json
│   ├── .env.example
│   ├── seed.js
│   └── src/
│       ├── index.js
│       ├── config/db.js
│       ├── models/Activity.js
│       ├── models/LedgerEntry.js
│       ├── routes/health.routes.js
│       ├── routes/activity.routes.js
│       ├── routes/ledger.routes.js
│       ├── routes/admin.routes.js
│       ├── middleware/adminAuth.js
│       └── utils/activityRules.js
└── client/
    ├── package.json
    ├── .env.example
    ├── index.html
    └── src/
        ├── main.jsx
        ├── App.jsx
        ├── api.js
        ├── index.css
        ├── components/
        └── pages/
            ├── HomePage.jsx
            ├── SubmitActivityPage.jsx
            ├── DashboardPage.jsx
            ├── RewardsPage.jsx
            └── AdminPage.jsx
```

------------------------------------------------------------------------

## 5. Data model and point rules

### Activity model

Use one Mongoose `Activity` model with fields similar to:

``` js
{
  activityId: String,       // unique readable ID, e.g. CG-ABC123
  citizenId: String,        // fixed demo ID: DEMO-CITIZEN-001
  activityType: String,
  title: String,
  description: String,
  area: String,
  pointsAwarded: Number,    // starts at 0
  status: String,           // PENDING, APPROVED, REJECTED
  createdAt: Date,
  updatedAt: Date
}
```

Allowed `activityType` values: - `WASTE_SEGREGATION` -
`PUBLIC_TRANSPORT` - `CLEANLINESS_DRIVE` - `GREEN_COMMUNITY` - `OTHER`

Allowed statuses: - `PENDING` - `APPROVED` - `REJECTED`

### LedgerEntry model

``` js
{
  entryId: String,
  citizenId: String,
  activityId: String,
  type: String,             // CREDIT, DEBIT, ADJUSTMENT
  points: Number,
  description: String,
  createdAt: Date
}
```

For the MVP, implement credit entries only unless there is a compelling
reason to do more. Public users must not be able to create arbitrary
ledger entries or adjust their own points.

Use one fixed demo citizen identifier (`DEMO-CITIZEN-001`). Do not build
full user authentication.

### Illustrative points table

Keep the rules in a server-side utility, not in the frontend.

  Activity type                Demo points
  -------------------------- -------------
  Waste segregation                     10
  Public transport use                   5
  Cleanliness drive                     20
  Green community activity              15
  Other approved activity                5

These values are illustrative demo values, not official rewards.

------------------------------------------------------------------------

## 6. API contract

Implement these endpoints and document example requests and responses in
root `CONTRACT.md`. Keep response shapes consistent and return clear
errors.

### Public endpoints

#### `GET /api/health`

Response:

``` json
{ "ok": true, "service": "civicgreen-api" }
```

#### `POST /api/activities`

Request:

``` json
{
  "activityType": "WASTE_SEGREGATION",
  "title": "Household waste segregation",
  "description": "Separated wet and dry waste today.",
  "area": "Demo residential area"
}
```

Create a new activity with a generated `activityId`, fixed demo
`citizenId`, `PENDING` status, `pointsAwarded: 0`, and timestamps.
Validate required fields, reasonable string lengths, and allowed
activity types. Do not trust frontend values for status, points, or
citizen ID.

Response shape:

``` json
{
  "activity": {
    "activityId": "CG-ABC123",
    "activityType": "WASTE_SEGREGATION",
    "title": "Household waste segregation",
    "description": "Separated wet and dry waste today.",
    "area": "Demo residential area",
    "pointsAwarded": 0,
    "status": "PENDING",
    "createdAt": "2026-10-25T10:00:00.000Z"
  }
}
```

The ID and timestamp above are examples, not hardcoded values.

#### `GET /api/activities`

Return the newest saved activities first.

Response:

``` json
{ "activities": [] }
```

The array must contain database records, not hardcoded examples.

#### `GET /api/activities/:activityId`

Return the requested activity, or HTTP 404 if it does not exist.

Response:

``` json
{
  "activity": {
    "activityId": "CG-ABC123",
    "activityType": "WASTE_SEGREGATION",
    "title": "Household waste segregation",
    "description": "Separated wet and dry waste today.",
    "area": "Demo residential area",
    "pointsAwarded": 0,
    "status": "PENDING",
    "createdAt": "2026-10-25T10:00:00.000Z"
  }
}
```

#### `GET /api/ledger`

Return the demo citizen's ledger entries and current balance. Calculate
the balance from persisted ledger entries; do not trust a balance
supplied by the frontend.

Response:

``` json
{
  "balance": 0,
  "entries": []
}
```

### Demo admin endpoint

All admin endpoints must require header `x-admin-password` matching the
server-side `ADMIN_PASSWORD`.

#### `PATCH /api/admin/activities/:id`

Request:

``` json
{ "status": "APPROVED" }
```

Allowed transitions: - `PENDING` → `APPROVED` - `PENDING` → `REJECTED`

Reject invalid transitions and repeated approval attempts.

On approval: 1. Load the activity and confirm it is still pending. 2.
Calculate points using the server-side points table. 3. Create exactly
one ledger credit. 4. Update the activity status and `pointsAwarded`. 5.
Prevent duplicate credits if the request is retried or two approval
requests arrive close together.

Use a MongoDB transaction if the configured deployment supports
transactions. If transactions are unavailable, use a robust idempotency
strategy and explain its limitations; do not claim atomic transaction
safety unless it is actually implemented and tested.

Return: - `401` for missing or incorrect admin password. - `404` for an
activity that does not exist. - `400` for invalid status or status
transition. - A clear server error for unexpected failures.

Example success response:

``` json
{
  "activity": {
    "_id": "example-database-id",
    "activityId": "CG-ABC123",
    "status": "APPROVED",
    "pointsAwarded": 10
  }
}
```

------------------------------------------------------------------------

## 7. Environment variables and secrets

Create `server/.env.example`:

``` env
PORT=5000
MONGO_URI=mongodb+srv://YOUR_USER:YOUR_PASSWORD@YOUR_CLUSTER/YOUR_DATABASE
ADMIN_PASSWORD=change-this-for-local-demo
CLIENT_ORIGIN=http://localhost:5173
```

Create `client/.env.example`:

``` env
VITE_API_URL=http://localhost:5000
```

Rules: - Create local `.env` files only when needed; never invent real
credentials. - Add `.env`, `node_modules`, `dist`, and local logs to
`.gitignore`. - Never expose `MONGO_URI` or `ADMIN_PASSWORD` through a
`VITE_*` variable or frontend code. - Never commit secrets. - If a
secret is accidentally committed, revoke/rotate it; deleting it in a
later commit is not enough. - If MongoDB is unavailable, show a clear
setup error and do not pretend data was saved.

------------------------------------------------------------------------

## 8. Build chunks --- one at a time

### CHUNK 0: Foundation and API contract

**Goal:** A runnable skeleton, database models, API endpoints, and
documented contract.

Tasks: 1. Inspect the workspace and report the proposed changes before
modifying anything. 2. Wait for approval before editing files or
installing packages. 3. Set up `/server` using Express, CORS, dotenv,
Mongoose, database connection handling, and clear error handling. 4. Set
up `/client` using Vite, React, and React Router. 5. Add the `Activity`
and `LedgerEntry` models. 6. Implement health, activity, ledger, and
admin endpoints. 7. Add server-side input validation and activity point
rules. 8. Add root `CONTRACT.md` with example request/response JSON for
every endpoint. 9. Add `server/seed.js` and an `npm run seed` command
with 5--8 fictional activities across different categories and statuses.
Seed ledger entries must be consistent with approved activities. 10. Add
`.gitignore` and `.env.example` files. 11. Add `client/src/api.js` with
one function per API endpoint and a base URL from `VITE_API_URL`. 12.
Add minimal placeholder routes/pages so the frontend runs. 13. Do not
add Gemini, a polished UI, real rewards, or deployment configuration.

Completion checks: - `GET /api/health` returns the exact documented
shape. - Backend and frontend start using documented commands. - MongoDB
connects when a valid `MONGO_URI` is configured. - Activity
create/list/detail endpoints work and validate input. - Ledger returns a
correct balance from stored entries. - Admin authentication and status
validation work. - Duplicate approval cannot create duplicate points. -
Seed command inserts fictional, internally consistent records. - `.env`
is ignored by Git.

After Chunk 0, explain how to run both apps in separate terminals, how
to test each endpoint, and which environment variables are needed.
**STOP.**

### CHUNK 1: Activity submission and public list

**Goal:** A citizen can submit and view civic activities end to end.

Tasks: 1. Build a simple responsive homepage explaining CivicGreen. 2.
Create a form with activity type, title, description, and locality. 3.
Submit to `POST /api/activities`. 4. Show a success state with the
activity ID and `PENDING` status. 5. Clearly label the record as part of
a hackathon demo. 6. Build an activity list showing activity type, area,
date, status, and points awarded. 7. Add loading, empty, validation, and
error states. Do not silently swallow API errors. 8. Do not allow the
frontend to choose its own points or initial status.

Test: - Submit a waste-segregation activity and confirm it appears in
the list. - Refresh the browser and verify it persists in MongoDB. -
Submit blank fields and verify validation errors appear. - Confirm
pending activities have zero points. - Confirm database errors are shown
clearly.

After Chunk 1, explain how to run and test it. **STOP.**

### CHUNK 2: Citizen dashboard and ledger

**Goal:** Citizens can see their points balance and the history behind
it.

Tasks: 1. Create a dashboard for the fixed demo citizen. 2. Display the
current points balance from `GET /api/ledger`. 3. Show the user's
activity history and statuses. 4. Display ledger entries with date,
description, activity reference, and points. 5. Refresh the dashboard
after an activity is approved or when the user manually refreshes. 6.
Explain that points are demo credits, not money. 7. Add summary counts
for pending, approved, and rejected activities only if straightforward.

Test: - A pending activity must not increase the balance. - Approving an
activity must create a ledger credit. - The balance must equal the sum
of ledger entries. - Refreshing must show persisted data. - Repeated
approval must not create duplicate credits.

After Chunk 2, explain the changes and tests. **STOP.**

### CHUNK 3: Demo admin dashboard

**Goal:** Demonstrate how a civic coordinator might review submitted
activities.

Tasks: 1. Build an `/admin` page with a simple password gate. 2. Keep
the password in memory or session storage only. 3. Send it using the
`x-admin-password` header. 4. List activities newest first. 5. Add
approve and reject actions for pending activities. 6. Show the expected
points before approval. 7. Ask for confirmation before changing a
status. 8. Handle wrong passwords, missing records, invalid transitions,
and server errors. 9. Display: "Demo admin --- not connected to a
municipality."

Test: - Correct password allows admin requests. - Wrong password is
rejected. - Approving an activity creates one credit. - Rejecting an
activity awards zero points. - A second approval attempt cannot add
another credit. - The public dashboard shows updated statuses and
balance after refresh.

After Chunk 3, explain the changes and tests. **STOP.**

### CHUNK 4: Green rewards catalogue

**Goal:** Show how green credits might be used in a future real-world
programme without pretending that rewards are active.

Tasks: 1. Create a simple rewards page. 2. Show illustrative rewards
such as a local-business discount, community event voucher, or simulated
utility rebate. 3. Display each reward's illustrative points cost. 4.
Clearly label every item: "Simulation only --- not redeemable." 5. Do
not deduct points, issue vouchers, call payment APIs, or claim
redemption has happened. 6. Add a brief explanation of what would be
required to connect real participating businesses or municipal
programmes in the future.

Test: - The catalogue loads successfully. - All reward entries are
clearly marked as simulations. - No real transaction or point deduction
can occur. - Do not display a non-functional Redeem button; if a visual
button is necessary, it must be disabled and labelled as unavailable in
the demo.

After Chunk 4, explain the changes and tests. **STOP.**

### CHUNK 5: UX polish and demo readiness

**Goal:** Make the prototype clear, accessible, and visually consistent.

Tasks: 1. Use consistent spacing, typography, and colors. 2. Make the
application usable on desktop and mobile. 3. Add clear activity type
labels, status badges, and point displays. 4. Add a short "How it works"
section: Submit activity → Review → Approve → Earn demo points. 5.
Explain the distinction between submitted activities, approved
activities, and credited points. 6. Show a visible prototype disclaimer.
7. Ensure forms have visible labels and buttons are keyboard-friendly.
8. Provide useful loading, empty, and error states. 9. Remove broken
links and non-functional controls. 10. Avoid unnecessary animations,
maps, charts, and decorative features that do not improve the demo.

Test: - Test at desktop and mobile widths. - Complete the flow from
activity submission to admin approval to ledger. - Check the browser
console and backend terminal for errors. - Confirm no button falsely
claims that a real-world reward was redeemed.

After Chunk 5, explain the final demo flow. **STOP.**

### CHUNK 6: Optional feature

Only if the core end-to-end flow works and time remains, choose **at
most one**: - A small dashboard summary of activity and points
statistics. - CSV export of the current demo activity list. - A simple
Gemini-generated suggestion for environmentally friendly activities.

Before starting: 1. Explain the trade-off and time cost. 2. Explain
which files will change. 3. Wait for my confirmation.

If Gemini is chosen: - Call Gemini only from the Express backend. - Keep
`GEMINI_API_KEY` server-side. - Validate model output and handle missing
keys, errors, and rate limits. - Keep the core application usable
without Gemini. - Do not send sensitive personal information to the
model.

Do not start this chunk unless the core flow works. **STOP after
completion.**

### CHUNK 7: Submission checklist

Do this only when I explicitly ask. Do not deploy unless I explicitly
ask.

Tasks: 1. Verify both apps start from clean terminals using documented
commands. 2. Run the main API tests and the complete UI flow. 3. Check
`.gitignore` and ensure `.env` files and secrets are not staged. 4. Add
or update `README.md` with the problem, solution, features, stack, local
setup, environment variables, API endpoints, and limitations. 5. Provide
a 60-second hackathon demo script. 6. Explain how points are calculated
and how duplicate credits are prevented. 7. Clearly list which features
were actually tested and which remain incomplete. 8. Confirm the
repository includes source code, `PROJECT.md`, `CONTRACT.md`,
`.env.example`, and `README.md`, but no secrets or `node_modules`. 9. Do
not deploy unless I explicitly request it.

------------------------------------------------------------------------

## 9. Local setup and smoke tests

Run the backend and frontend in separate terminals.

### Backend

``` bash
cd server
npm install
npm run dev
```

If a `dev` script is needed, prefer Node's built-in watch mode where
supported. Add `nodemon` only if necessary.

### Frontend

``` bash
cd client
npm install
npm run dev
```

### PowerShell smoke tests

Health:

``` powershell
Invoke-RestMethod http://localhost:5000/api/health
```

Create an activity:

``` powershell
$body = @{
  activityType = "WASTE_SEGREGATION"
  title = "Household waste segregation"
  description = "Separated wet and dry waste today."
  area = "Demo residential area"
} | ConvertTo-Json

$created = Invoke-RestMethod -Method Post `
  -Uri http://localhost:5000/api/activities `
  -ContentType "application/json" -Body $body

$created
```

List activities:

``` powershell
Invoke-RestMethod http://localhost:5000/api/activities
```

Get an activity by its returned activity ID:

``` powershell
Invoke-RestMethod http://localhost:5000/api/activities/CG-ABC123
```

Read ledger:

``` powershell
Invoke-RestMethod http://localhost:5000/api/ledger
```

Approve an activity (replace the database ID and password with the
configured local demo values):

``` powershell
$headers = @{ "x-admin-password" = "change-this-for-local-demo" }
$body = @{ status = "APPROVED" } | ConvertTo-Json

Invoke-RestMethod -Method Patch `
  -Uri http://localhost:5000/api/admin/activities/REPLACE_WITH_DATABASE_ID `
  -Headers $headers -ContentType "application/json" -Body $body
```

Use the actual MongoDB `_id` returned by the list endpoint for the admin
route. Example IDs above are placeholders.

------------------------------------------------------------------------

## 10. GitHub rules

Create a new repository named `civicgreen` before development if the
user has not already created one.

Make meaningful commits as real working chunks are completed. Suggested
examples:

1.  `chore: scaffold client and server`
2.  `feat: add activity and ledger APIs`
3.  `feat: build activity submission flow`
4.  `feat: add citizen points dashboard`
5.  `feat: add admin approval workflow`
6.  `feat: add simulated green rewards`
7.  `docs: add setup and hackathon demo instructions`

Do not make empty commits just to reach a minimum count. Never commit
`.env`, credentials, `node_modules`, or generated build directories. Do
not push to GitHub or run Git commands unless the user explicitly
requests or approves the action.

------------------------------------------------------------------------

## 11. Definition of done

The MVP is ready when: - A citizen can submit a civic activity and
receive an activity ID. - The activity is saved in MongoDB. - The
activity remains pending until reviewed. - A demo admin can approve or
reject the activity. - Approval creates exactly one valid ledger
credit. - Duplicate approval cannot create duplicate points. - The
citizen can view the updated balance and ledger. - The rewards catalogue
clearly marks rewards as simulations. - Both frontend and backend run
locally using documented commands. - The complete flow has been tested
and limitations are clearly documented.

**Final rule:** Work only on the current chunk. Inspect first, propose
changes, wait for approval, implement, test, explain, and stop. Never
start a later chunk without explicit confirmation.

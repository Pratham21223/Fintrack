# FinTrack — Full-Stack Finance Dashboard

A role-based financial records management system with dashboard analytics, built with the MERN stack.

---

## Overview

- **Backend** — REST API with JWT authentication, role-based access control (viewer / analyst / admin), transaction CRUD with soft delete, and dashboard analytics powered by MongoDB aggregation pipelines.
- **Frontend** — React single-page application with protected routes, transaction management (filters, search, pagination), interactive dashboard charts, and admin user management.
- **Docs** — Interactive API documentation site with searchable pages, endpoint references, concept guides, and architecture walkthroughs.

---

## Monorepo Structure

```
fintech/
├── backend/     # Express REST API (Node.js, MongoDB)
├── frontend/    # React SPA (Vite, TailwindCSS, Recharts)
└── docs/        # API documentation site (React, Tailwind)
```

---

## Tech Stack

| Layer        | Technologies                                                                                         |
| ------------ | ---------------------------------------------------------------------------------------------------- |
| **Backend**  | Node.js, Express 5, MongoDB (Mongoose 9), JWT, bcrypt, express-validator, helmet, morgan, rate-limit |
| **Frontend** | React 19, Vite 8, TailwindCSS 4, React Router 7, Axios, Recharts, Lucide React, React Hot Toast      |
| **Docs**     | React 19, Vite 8, TailwindCSS 4, React Router 7, Lucide React                                        |

---

## Features

- **JWT Authentication** — Register, login, and profile endpoints with 7-day token expiry
- **3-Tier RBAC** — Viewer, Analyst, and Admin roles with middleware-level enforcement
- **Transaction CRUD** — Create, read, update, and soft-delete financial records (admin only for mutations)
- **Filtering & Search** — Filter by type, category, date range; full-text search across descriptions
- **Pagination** — Server-side pagination on transaction listings
- **Dashboard Analytics** — Summary totals, category breakdowns, monthly trends, and recent activity via MongoDB aggregation
- **User Management** — Admin can list users, change roles, and toggle active/inactive status
- **Responsive UI** — Mobile-friendly layout with sidebar navigation and role-based menu visibility
- **Interactive Docs** — Searchable documentation site with endpoint cards, code blocks, and concept pages
- **Security** — Helmet headers, rate limiting (100 req/15 min), input validation, global error handler

---

## Getting Started

### Prerequisites

- Node.js (v18+)
- MongoDB Atlas account (or local MongoDB instance)

### 1. Clone the repository

```bash
git clone https://github.com/Pratham21223/Fintrack.git
cd fintech
```

### 2. Backend

```bash
cd backend
npm install
```

Create a `.env` file in `backend/`:

```env
MONGO_URL=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/fintech
PORT=3000
JWT_SECRET=your-secret-key-here
```

Seed the database and start the server:

```bash
npm run seed    # Populates DB with test users + 20 sample transactions
npm run dev     # Starts on http://localhost:3000
```

### 3. Frontend

```bash
cd frontend
npm install
npm run dev     # Starts on http://localhost:5173 (proxied to backend)
```

### 4. Docs

```bash
cd docs
npm install
npm run dev     # Starts on http://localhost:5174
```

---

## Test Credentials

Run `npm run seed` in the backend to create these accounts:

| Email            | Password   | Role    |
| ---------------- | ---------- | ------- |
| admin@test.com   | admin123   | Admin   |
| analyst@test.com | analyst123 | Analyst |
| viewer@test.com  | viewer123  | Viewer  |

---

## API Endpoints

All endpoints are prefixed with `/api`. Protected routes require `Authorization: Bearer <token>`.

### Auth (`/api/auth`)

| Method | Path        | Auth | Description                     |
| ------ | ----------- | ---- | ------------------------------- |
| POST   | `/register` | No   | Create a new user (role=viewer) |
| POST   | `/login`    | No   | Login and receive JWT token     |
| GET    | `/profile`  | Yes  | Get current user's profile      |

### Transactions (`/api/transactions`)

| Method | Path   | Auth | Role  | Description                                     |
| ------ | ------ | ---- | ----- | ----------------------------------------------- |
| GET    | `/`    | Yes  | All   | List transactions (filters, search, pagination) |
| GET    | `/:id` | Yes  | All   | Get a single transaction                        |
| POST   | `/`    | Yes  | Admin | Create a new transaction                        |
| PUT    | `/:id` | Yes  | Admin | Update a transaction                            |
| DELETE | `/:id` | Yes  | Admin | Soft-delete a transaction                       |

### Dashboard (`/api/dashboard`)

| Method | Path                  | Auth | Role           | Description                         |
| ------ | --------------------- | ---- | -------------- | ----------------------------------- |
| GET    | `/summary`            | Yes  | Analyst, Admin | Total income, expenses, net balance |
| GET    | `/category-breakdown` | Yes  | Analyst, Admin | Totals grouped by category + type   |
| GET    | `/monthly-trend`      | Yes  | Analyst, Admin | Income vs expense per month (12mo)  |
| GET    | `/recent`             | Yes  | Analyst, Admin | Last 5 transactions                 |

### Users (`/api/users`)

| Method | Path          | Auth | Role  | Description            |
| ------ | ------------- | ---- | ----- | ---------------------- |
| GET    | `/`           | Yes  | Admin | List all users         |
| PATCH  | `/:id/role`   | Yes  | Admin | Change a user's role   |
| PATCH  | `/:id/status` | Yes  | Admin | Toggle active/inactive |

---

## Permission Matrix

| Action                       | Viewer | Analyst | Admin |
| ---------------------------- | :----: | :-----: | :---: |
| Register / Login             |   ✅   |   ✅    |  ✅   |
| View own profile             |   ✅   |   ✅    |  ✅   |
| View transactions            |   ✅   |   ✅    |  ✅   |
| Search & filter transactions |   ✅   |   ✅    |  ✅   |
| View dashboard analytics     |   ❌   |   ✅    |  ✅   |
| Create transactions          |   ❌   |   ❌    |  ✅   |
| Update transactions          |   ❌   |   ❌    |  ✅   |
| Delete transactions          |   ❌   |   ❌    |  ✅   |
| List all users               |   ❌   |   ❌    |  ✅   |
| Change user roles            |   ❌   |   ❌    |  ✅   |
| Toggle user status           |   ❌   |   ❌    |  ✅   |

---

## Project Structure

```
backend/
├── config/
│   └── db.js                     # MongoDB connection setup
├── controllers/
│   ├── authController.js         # Register, login, profile
│   ├── transactionController.js  # CRUD for financial records
│   ├── dashboardController.js    # Aggregation pipelines
│   └── userController.js         # User management (admin)
├── middlewares/
│   ├── authMiddleware.js         # JWT verification
│   ├── rbac.js                   # Role-based access control
│   └── validate.js               # express-validator error handler
├── models/
│   ├── userModel.js              # User schema + bcrypt hooks
│   └── transactionModel.js       # Transaction schema + indexes
├── routes/
│   ├── authRoute.js              # /api/auth/*
│   ├── transactionRoute.js       # /api/transactions/*
│   ├── dashboardRoute.js         # /api/dashboard/*
│   └── userRoutes.js             # /api/users/*
├── seeds/
│   └── seed.js                   # Database seeding script
├── utils/
│   └── apiError.js               # Custom error class
├── index.js                      # App entry point
└── package.json

frontend/
├── src/
│   ├── api/axios.js              # Axios instance + JWT interceptors
│   ├── context/AuthContext.jsx    # Auth state (login, register, logout)
│   ├── components/
│   │   ├── Layout.jsx            # Sidebar + topbar shell
│   │   ├── ProtectedRoute.jsx    # Auth & role guard
│   │   ├── RoleGate.jsx          # Conditional render by role
│   │   └── Spinner.jsx           # Loading indicator
│   ├── pages/
│   │   ├── LoginPage.jsx         # Login form
│   │   ├── RegisterPage.jsx      # Registration form
│   │   ├── TransactionsPage.jsx  # Transaction list + filters
│   │   ├── TransactionForm.jsx   # Create / edit transaction
│   │   ├── DashboardPage.jsx     # Charts + summary cards
│   │   └── UsersPage.jsx         # User management table
│   └── App.jsx                   # Route definitions
└── vite.config.js                # Vite + Tailwind + API proxy

docs/
├── src/
│   ├── components/               # Layout, CodeBlock, EndpointCard, SearchModal, etc.
│   ├── pages/                    # HomePage, QuickStart, API docs, Concepts, Architecture
│   └── App.jsx                   # Docs route definitions
└── vite.config.js
```

---

## Design Decisions

- **Soft delete** — Transactions are never permanently removed; `DELETE` sets `isDeleted: true`. All queries filter `{ isDeleted: false }`.
- **Default role** — New users are assigned `viewer`. Only admins can promote to `analyst` or `admin`.
- **Real-time deactivation** — The auth middleware re-queries the database on every request to check if the user is still active and reads the latest role, so admin changes take effect immediately.
- **Global records** — Financial records are shared across all users (not per-user ownership). Access is controlled by role, not record ownership.
- **JWT expiry** — Tokens expire after 7 days. No refresh token flow is implemented.
- **Rate limiting** — 100 requests per 15-minute window per IP via `express-rate-limit`.
- **Input validation** — All routes use `express-validator` with a shared `validate` middleware that returns structured 422 errors.
- **Consistent response envelope** — Every endpoint returns `{ success, message, data }` (or `{ success, message, errors }` for validation failures).

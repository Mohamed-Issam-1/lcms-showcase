# Language Center Management System (LCMS)

LCMS is a multi-tenant web platform for managing the academic, administrative, financial, and reporting operations of language centers.

The platform combines a role-aware Filament administration panel with dedicated React/Inertia experiences for teachers and students.

> **Status:** The MVP feature set is implemented and currently undergoing validation, UI refinement, security hardening, and release-readiness work.

---

## Overview

LCMS supports the complete operational lifecycle of a language center:

```text
Registration
    ↓
Administrative Review & Account Provisioning
    ↓
Student Enrollment
    ↓
Course Classes
    ↓
Schedules & Sessions
    ↓
Attendance
    ↓
Fees & Payments
    ↓
Reports & Dashboards
    ↓
Audit & Operational Review
```

The system is multi-tenant by design. Center-owned records remain inside their tenant boundary, while branch-level operations apply authoritative branch scoping where required.

---

## Key Capabilities

### Platform Administration

- Language-center management
- Center Owner account management
- Subscription plans and lifecycle management
- Plan versions, pricing, and feature entitlements
- Center subscription assignment and history
- Platform-level reporting
- Feature catalog management

### Center & Branch Operations

- Branch and classroom management
- Branch Manager and Finance Employee assignments
- Staff operational lifecycle management
- Role-aware dashboards and quick actions

### Academic Management

- Languages and academic levels
- Courses and prerequisites
- Course classes
- Teacher and classroom assignments
- Student enrollments
- Enrollment lifecycle and history

### Scheduling

- Recurring class schedules
- Generated class sessions
- Teacher and classroom conflict detection
- Session rescheduling, cancellation, and completion
- Historical session preservation

### Attendance

- Configurable attendance statuses
- Attendance recording and correction
- Late-minute tracking
- Enrollment attendance calculations
- Student attendance history
- Teacher attendance rosters

### Finance

- Enrollment fees
- Installments
- Payments and allocations
- Student balances
- Payment receipts
- Fee voiding
- Payment reversals
- Historical financial ownership
- Branch-scoped financial operations

### Reporting

Operational reports cover:

- Academics
- Enrollments
- Attendance
- Finance

Supported exports:

- CSV
- PDF

Report datasets are rebuilt through authorized backend services at export time.

### Audit

Sensitive operations generate append-only audit records with actor, tenant, branch, subject, timestamp, and before/after context. Sensitive credential and token values are redacted.

---

## User Roles

LCMS uses six fixed system roles.

| Role | Primary Scope |
| --- | --- |
| **Platform Owner** | Platform administration, centers, subscriptions, and platform reporting |
| **Center Owner** | Full administration inside one language center |
| **Branch Manager** | Operational management inside an assigned branch |
| **Finance Employee** | Financial operations inside an assigned branch |
| **Teacher** | Assigned classes, sessions, attendance, and teacher portal |
| **Student** | Own courses, schedule, attendance, finance, and student portal |

Each User Account has exactly one system role.

A Person may have multiple User Accounts when separate operational roles are required. Permissions from those accounts are never combined automatically.

---

## Interfaces

### Filament Administration Panel

The Filament panel serves authorized administrative and operational roles.

Current areas include:

- Centers and Center Owners
- Branches and Classrooms
- Students and Staff
- User Accounts
- Registration Requests
- Languages, Academic Levels, and Courses
- Course Classes and Enrollments
- Class Schedules and Sessions
- Attendance Statuses and Attendances
- Enrollment Fees, Payments, and Student Balances
- Reports and Audit Records
- Features
- Subscription Plans, Versions, Prices, and Entitlements
- Center Subscriptions

The administrative UI uses record-specific headings, contextual summaries, standardized actions, responsive data tables, and role-aware navigation.

Navigation visibility is not an authorization boundary. Backend authorization remains authoritative.

### React / Inertia Application

The account-facing application includes:

- Login and logout
- Login-success routing
- Forced first-login password change
- Password recovery
- Active-session management
- Teacher dashboard and portal
- Student dashboard and portal
- Profile and security management

Administrative accounts are routed to the authorized Filament panel.

---

## Authentication & Account Lifecycle

LCMS uses a system-generated account login identifier rather than email as the primary login credential.

Controls include:

- 8-digit account login identifiers
- Temporary passwords
- Forced first-login password changes
- Account activation and deactivation
- Failed-login tracking and temporary lockout
- Active-session management
- Recovery-email separation from login identity
- 5-digit OTP password recovery
- Reissued temporary credentials

Public registration does **not** create a User Account directly.

```text
Public Registration
        ↓
Pending Registration Request
        ↓
Administrative Review
        ↓
Role / Branch Selection
        ↓
Approval
        ↓
Person + Operational Record + User Account
```

---

## Multi-Tenancy & Authorization

Core authorization principles:

- Backend authorization is authoritative.
- Frontend visibility is never treated as authorization.
- Center-owned records must remain inside their tenant boundary.
- Branch-scoped operations validate authoritative branch context.
- Branch Manager access requires an active persisted assignment.
- Finance Employee access requires an active persisted assignment.
- Cross-center resources are rejected.
- Persisted database state is used instead of trusting mutated in-memory models.
- Financial history follows historical financial ownership where required.
- Sensitive mutations use transactions where appropriate.

---

## Subscription & Feature Access

LCMS includes a versioned subscription domain with:

- Subscription plans and lifecycle states
- Versioned plan contracts
- Monthly and yearly pricing
- Boolean, limit, and configuration entitlements
- Current and historical center subscriptions
- Trialing, active, past-due, and terminal states
- Subscription event history
- Feature-access enforcement
- Capacity enforcement

Published plan versions preserve their commercial contract for existing subscriptions.

---

## Technology Stack

### Backend

- PHP 8.2+
- Laravel 12
- Eloquent ORM
- Laravel Sanctum
- MySQL / MariaDB
- Dompdf

### Administration

- Filament 5
- Livewire

### Frontend

- React 18
- TypeScript
- Inertia.js 2
- Tailwind CSS
- Vite
- Lucide React

### Quality & Testing

- PHPUnit
- Laravel Unit and Feature tests
- Authorization tests
- Tenant-isolation tests
- Branch-isolation tests
- Filament / Livewire tests
- Integration tests
- Release-flow tests
- Authentication and recovery-flow tests

---

## Project Structure

```text
app/
├── Filament/
├── Http/
├── Models/
├── Policies/
└── Services/

database/
├── factories/
├── migrations/
├── schema/
└── seeders/

docs/
└── release/

resources/
├── css/
├── js/
└── views/

routes/
tests/
```

---

## Local Development Requirements

Install:

- PHP 8.2 or newer
- Composer
- MySQL or MariaDB
- Node.js
- npm

Exact dependencies are defined in `composer.json` and `package.json`.

---

## Installation

```bash
git clone https://github.com/Mohamed-Issam-1/language-center-management-system.git
cd language-center-management-system
composer install
```

Create the environment file.

Windows:

```powershell
Copy-Item .env.example .env
```

Linux/macOS:

```bash
cp .env.example .env
```

Generate the application key:

```bash
php artisan key:generate
```

Configure the database in `.env`, create the database, then run:

```bash
php artisan migrate
php artisan db:seed --class=RoleSeeder
```

Install and build frontend dependencies:

```bash
npm install
npm run build
```

For local development:

```bash
composer run dev
```

---

## Demo & UAT Data

A deterministic demo dataset is available through:

```text
database/seeders/DemoSeeder.php
```

Run it only against a disposable local, demo, or UAT database:

```bash
php artisan db:seed --class=DemoSeeder
```

The demo seeder is separate from the normal `DatabaseSeeder`, refuses production, uses fictional deterministic data, covers the major operational domains, and is idempotent when the expected complete state already exists.

Additional documentation:

```text
docs/release/demo-uat-data.md
```

Never commit real QA/UAT credentials, production data, or production database dumps.

---

## Testing

Run the complete test suite:

```bash
composer test
```

or:

```bash
php artisan test
```

Run a specific test file:

```bash
php artisan test tests/Feature/<ActualTestFile>.php
```

Additional validation commands:

```bash
npm run build
composer validate
composer audit
npm audit
```

The release workflow also uses targeted checks for authorization, tenancy, branch isolation, database constraints, lifecycle operations, reporting, authentication, Filament behavior, and end-to-end operational flows.

---

## Database Schema Snapshot

The repository includes:

```text
database/schema/mysql-schema.sql
```

The snapshot reduces setup time during automated testing and clean-environment validation. It must remain synchronized with migrations whenever the database structure changes.

---

## Production Notes

Production environments must use an untracked environment-specific `.env`.

Recommended baseline:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-production-domain.example

SESSION_DRIVER=database
SESSION_SECURE_COOKIE=true
SESSION_HTTP_ONLY=true
SESSION_SAME_SITE=lax

MAIL_MAILER=smtp
```

Production deployments should also use a unique `APP_KEY`, protected database credentials, HTTPS, secure session cookies, a real mail transport, environment-appropriate logging, private storage, and external backups.

Registration personal pictures are stored on Laravel's private local disk and are served only through authorized application endpoints.

---

## Health Check

```text
/up
```

---

## Backup & Restore

Backup and restore procedures are documented in:

```text
docs/release/backup-restore.md
```

Backups must remain outside the public web root and outside the Git repository.

---

## Release Documentation

Release and UAT documentation is maintained under:

```text
docs/release/
```

Key documents include:

```text
docs/release/release-readiness.md
docs/release/demo-uat-data.md
docs/release/uat-checklist.md
docs/release/backup-restore.md
```

---

## Security Notes

Never commit:

- `.env`
- database or mail credentials
- API credentials
- authentication tokens
- recovery codes
- OTP values
- private keys
- production database dumps
- private user files
- real QA/UAT credentials

Authentication, tenant context, branch context, persisted assignments, account lifecycle state, feature access, and backend permissions remain authoritative regardless of frontend visibility.

---

## Development Workflow

```text
feature branch
      ↓
Pull Request / Review
      ↓
develop
      ↓
release validation
      ↓
main
```

`develop` is the integration branch for validated development work. Feature development should normally occur on dedicated feature branches rather than directly on `main`.

---

## Repository

https://github.com/Mohamed-Issam-1/language-center-management-system

---

## Project Goal

LCMS aims to provide language centers with a structured, secure, tenant-aware system that unifies student administration, academics, scheduling, attendance, finance, reporting, subscriptions, and operational auditing while keeping business rules and authorization centralized in the backend.

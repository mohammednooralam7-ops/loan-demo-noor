# Loan Management & Disbursement Demo (Laravel)

A demo loan workflow system built with **sample data only**. It shows approval, disbursement and reconciliation flows with role-based access, an audit trail, and a high-volume import command.

> Not affiliated with any employer. No real customer or company data.

## Features
- Loan application -> review -> approve/reject -> disburse -> reconcile
- Role-based access: `sales`, `risk`, `audit`, `recovery`, `admin`
- Immutable audit log of every status change
- Idempotent disbursement API (`Idempotency-Key` header)
- Queue-based bulk import (`php artisan loans:import-demo 50000`) to show 50K-record handling
- Feature tests for the workflow

## Stack
Laravel 11, PHP 8.2+, MySQL, queues (database driver)

## Setup
```bash
composer create-project laravel/laravel loan-demo
cd loan-demo
# copy the contents of this package's app/, database/, routes/, tests/ into the project (merge folders)
cp .env.example .env && php artisan key:generate
# set DB_* in .env, then:
php artisan migrate
php artisan queue:table && php artisan migrate
php artisan test
```

## API (auth via Laravel Sanctum token, add `composer require laravel/sanctum` and run `php artisan install:api`)
| Method | Endpoint | Role | Purpose |
|---|---|---|---|
| POST | /api/loans | sales | Create application |
| POST | /api/loans/{id}/approve | risk | Approve |
| POST | /api/loans/{id}/reject | risk | Reject |
| POST | /api/loans/{id}/disburse | admin | Disburse (idempotent) |
| POST | /api/loans/{id}/reconcile | audit | Mark reconciled |
| GET | /api/loans/{id}/audit | audit | View audit trail |

## Security notes
- Policy-based authorization per role
- Validation on every input; no mass-assignment of status fields
- Audit trail is append-only
- No secrets in the repo; use `.env`

## Roadmap
- Livewire 3 dashboard
- Reporting by department
- Docker compose

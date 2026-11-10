# TANYA BIO ENERGY – Cloud Web Portal (Deployment Preparation)

This is a **web-only, multi-user starter application**. Employees access it using a browser and an individual login. It is not an installed desktop program.

## Included modules

- Login and role-based server-side access control
- Admin, Accountant, Transport Manager, Supervisor and Driver roles
- Daily cash/bank transaction register and running balances
- Sales and purchase records with basic GST amount calculation and GSTIN fields
- Vehicle register with FC, insurance and PUC expiry dates
- Driver master and driver/vehicle trip register
- Driver accounts restricted to their linked driver's own trip history
- Monthly sales/purchase summary
- Password change, CSRF protection and basic audit log for created records
- PostgreSQL configuration, Gunicorn deployment and daily expiry-alert cron job
- Responsive browser layout for desktop and mobile

## Cloud deployment route

The project includes `render.yaml` as a deployment blueprint for Render.

1. Create a private GitHub repository.
2. Upload the contents of this folder to the repository (do not upload `.env`, database files or secrets).
3. Create a Render account and connect the repository.
4. Use Render Blueprint deployment and select `render.yaml`.
5. During setup, provide:
   - `BOOTSTRAP_ADMIN_USERNAME`
   - `BOOTSTRAP_ADMIN_PASSWORD` (unique, at least 16 characters recommended)
   - `SMTP_HOST`, `SMTP_USER`, `SMTP_PASSWORD`, `ALERT_EMAIL`
6. Review the selected hosting/database plan and recurring costs before confirming.
7. After deployment, open the generated HTTPS URL and log in as the bootstrap administrator.
8. Change the administrator password, create employee accounts and assign roles.
9. Test each role with separate test accounts before entering real business data.
10. Configure a custom domain only after the initial deployment and HTTPS checks.

**No website has been published by this package.** A Git repository, hosting account, domain decision and secret configuration are required to make it live.

## Roles

- Admin: all modules and user management.
- Accountant: accounts, sales, purchases and reports.
- Transport Manager: vehicles, drivers, trips and reports.
- Supervisor: read-only business views.
- Driver: trip entry and only trips associated with the driver's linked profile.

The backend checks permissions for protected routes. Hiding a navigation link alone is not treated as security.

## Expiry email alerts

The included Render cron job runs `python app.py send-alerts` daily at 03:00 UTC (08:30 India time). It sends a single email to `ALERT_EMAIL` for each vehicle document (FC, insurance, PUC) expiring within the next 30 days, and records the sent alert to avoid repeat emails on the same day. Configure valid SMTP settings. Test with a non-critical email before relying on it.

## Important limitations before production use

This is a **deployment-preparation starter, not a finished statutory accounting/GST ERP**. It is not yet ready for official books or GST filings. Before production use, a qualified developer and your accountant should complete and test:

- Proper double-entry accounting engine, chart of accounts, journal vouchers, contra entries, ledger statements and trial balance.
- Cash/bank opening balance setup and reconciliation; debit/credit balance presentation depends on account type and accounting rules.
- GST place-of-supply logic, intra/inter-state CGST/SGST/IGST split, HSN/SAC, invoice PDF, sequential invoice numbering, credit/debit notes, e-invoice/e-way bill integrations if applicable, and GSTR reconciliation.
- Edit/cancel/reversal workflows with immutable audit history; this starter currently focuses on creating records.
- Secure document uploads, document versioning, role-specific record ownership, granular permission administration and account deactivation workflows.
- Automated database backup/restore tests, monitoring, rate limiting, MFA for admins, secure session settings and security review.
- Correct tax treatment and approval workflows for freight, expenses and vehicle costs.
- Validate all reports and GST calculations with an Indian accountant before filing or relying on them.

## Local developer test

Python 3.11+:
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
set BOOTSTRAP_ADMIN_USERNAME=admin
set BOOTSTRAP_ADMIN_PASSWORD=use-a-long-test-password
python app.py
```
Then open `http://127.0.0.1:5000`. For production, use PostgreSQL and HTTPS, not the local SQLite development database.

# Database overview

FraudLens stores all application data in **PostgreSQL**, with schema changes managed through versioned migrations.
There is no in-memory or placeholder fallback: if the database is unavailable, the application reports an error.

| Domain | What is stored |
|---|---|
| Organizations & access | Organizations, users, configurable role permissions, revocable sessions, invitations |
| Data ingestion | Datasets, file imports with validation reports, and one record per source row — including rows set aside, with their reasons |
| Models & detection | Model versions with evaluation results, training runs, detection runs, scores, business rules and their execution history |
| Investigation | Alerts with explanations and priority breakdown, cases, investigation timelines and analyst decisions |
| Governance | Append-only audit history, generated reports |

**Isolation.** Every tenant-owned record carries its organization identifier; the API derives the organization from the
authenticated session and scopes every query to it. Automated tests verify that one organization cannot read or modify
another's records.

**Integrity.** Foreign keys connect every record to its organization and source; deleting an organization removes its
data, while references to users are preserved as history. A database trigger rejects any modification or deletion of
audit events.

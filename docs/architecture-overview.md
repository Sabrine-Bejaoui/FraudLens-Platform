# Architecture overview

```
Web application (React, English/French)
        │  HTTPS · JSON · session cookie
        ▼
API (FastAPI)
  ├─ Authentication, sessions, permissions, organization context
  ├─ Data ingestion: intake → column recognition → validation → loading → profiling
  ├─ Model registry & compatibility checks
  ├─ Detection: model scoring + business rules → prioritised, explained alerts
  ├─ Investigation: alerts, cases, decisions, timelines
  ├─ Intelligence, monitoring, reporting (PDF/CSV)
  └─ Background jobs for long operations (validation, loading, scoring, training)
        │
        ▼
PostgreSQL (versioned migrations, append-only audit history)
```

**Organization isolation.** The organization is derived from the authenticated session, never from the request;
every data access is scoped to it.

**Model lifecycle.** Reference models (public data) and organization models share one registry. New organization
models are registered as candidates, reviewed on held-out data, and approved by an authorised user before they can
be used. Every detection run records the model version, thresholds and compatibility result it used.

**Explainability.** Each model alert stores the SHAP contributions of its inputs and their position in the training
distribution; each rule alert stores the observed values that triggered the rule.

**Internationalisation.** All interface text comes from English and French resource files with enforced key parity;
reports are generated in the reader's chosen language.

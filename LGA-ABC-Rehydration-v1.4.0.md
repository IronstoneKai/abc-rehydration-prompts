# 🧠 ABC Rehydration Prompt — LGA Project

**Version:** v1.4.0  
**Date:** 2026-01-29  
**Author:** IronstoneKai  
**Associated Project:** lga-frontend  
**Governance System:** Agentic Business Chain (ABC)  
**Derived From:** Active ABC Session 2026-01-29

---

## 🧩 1. Purpose

This document continues from v1.3.0 and extends the validation of the **AI Runtime → Supabase** pipeline, focusing on:

1.  Successful insertion of `discovery_insights` records.
2.  Expansion of schema to persist full AI-generated outputs.
3.  Improved telemetry, observability, and DevOps governance.

It ensures that **Codex-Frontend**, **Codex-OS**, and the **Human CLI operator** work under consistent schema, migrations, and observability discipline while developing the **Discovery Insights Persistence MVP**.

---

## 🧭 2. Canonical Repositories and Artifacts

### **lga-frontend**

| Key Directories | Purpose |
|-----------------|---------|
| `/supabase/migrations/` | Source of truth for database structure and schema evolution |
| `/app/api/runtime/run/` | AI Runtime logic for discovery processing |
| `/docs/validation_notes/Phase_A-120_Runtime_Discovery_Persistence_and_Telemetry.md` | Runtime and telemetry follow-up documentation |

### **agentic-business-os**

| Key Directories | Purpose |
|-----------------|---------|
| `/docs/adr/ADR-037–ADR-040` | Governance and architecture decisions for Discovery Insights phase |
| `/docs/task_plans/` | Milestone and rollout tracking |

---

## 🧠 3. Agent Roles and Responsibilities

| Agent | Repository | Role | Core Tasks |
|-------|------------|------|------------|
| **Codex-Frontend** | `lga-frontend` | Engineering | Create migration scripts, implement telemetry hooks, expand runtime schema |
| **Codex-OS** | `agentic-business-os` | Governance | Create ADRs for alerting, telemetry, and dynamic runtime prompt config |
| **ABC Agent** | — | Orchestration | Maintain context across sessions and validate runtime telemetry logic |
| **Human CLI Operator** | Local | Validation | Execute Supabase CLI operations, confirm schema alignment, test Fly.io deployments |

---

## ⚙️ 4. Active Environment Context

- **Branch:** `feature/e2e-orchestration-validation`
- **Supabase Branch:** `orchestration-alignment-v8`
- **Fly App:** `lga-runtime`

| Environment | Supabase URL | DB URL | Notes |
|-------------|--------------|--------|-------|
| **Local** | `http://127.0.0.1:54322` | Dev sandbox | Destructive ops allowed |
| **Remote** | `https://czdvkgalxhvvmosbemsg.supabase.co` | `aws-1-us-east-1.pooler.supabase.com` | Production validation only |

---

## 🧱 5. Next Schema Evolution

**Objective:** expand `public.discovery_insights` to capture AI output.

### Migration Plan

```sql
ALTER TABLE public.discovery_insights
ADD COLUMN run_id uuid,
ADD COLUMN summary text,
ADD COLUMN details jsonb,
ADD COLUMN model text,
ADD COLUMN prompt_version text;
```

### Governance Requirements

1.  Created via `supabase migration new expand_discovery_insights_schema`.
2.  Committed under `/supabase/migrations/` in `feature/e2e-orchestration-validation`.
3.  Validated against `orchestration-alignment-v8` via `supabase db diff`.

---

## 🚨 6. Runtime Telemetry and Alerting

### Requirements

1.  Add `telemetry_events` entry for each failed insert or runtime exception.
2.  Create `/api/alerts/runtime` endpoint for error dispatch (future Slack/email integration).
3.  Define **ADR-038 "Runtime Alert and Telemetry Policy."**

### Expected new telemetry events

-   `runtime_discovery_started`
-   `runtime_discovery_completed`
-   `runtime_insert_failed`
-   `runtime_alert_triggered`

---

## 🔁 7. Discovery Campaign State Updates

When runtime completes:

```sql
UPDATE public.discovery_campaigns
SET status = 'Completed', completed_at = now()
WHERE id = <campaign_id>;
```

> Link inserted `discovery_insights` to campaign for display in the frontend.

---

## 💡 8. Dynamic Prompt and Model Configuration

Create a new table `runtime_configs` for dynamic prompt and model control:

```sql
CREATE TABLE public.runtime_configs (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text NOT NULL,
  model text NOT NULL,
  prompt_template text NOT NULL,
  temperature numeric DEFAULT 0.7,
  version text DEFAULT 'v1',
  created_at timestamptz DEFAULT now()
);
```

> Ensure runtime agents pull prompt and model dynamically from this table.

---

## 🧪 9. Validation Workflow

| Phase | Validation Action | Owner |
|-------|-------------------|-------|
| 1 | Confirm `discovery_insights` inserts | Human CLI |
| 2 | Apply schema expansion migration | Codex-Frontend |
| 3 | Validate `telemetry_events` capture on failures | Codex-Frontend |
| 4 | Record telemetry ADR and migration summary | Codex-OS |
| 5 | Test runtime with new model config | Human CLI + Codex-Frontend |

---

## 📄 10. Documentation and Governance Log

### New Documents Created

-   `/docs/validation_notes/Phase_A-120_Runtime_Discovery_Persistence_and_Telemetry.md`
-   `/supabase/migrations/<timestamp>_expand_discovery_insights_schema.sql`
-   `/docs/adr/ADR-038_Runtime_Telemetry_and_Alerting.md` (Codex-OS)

---

## 🪄 11. ABC Bootstrap Snippet

```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (engineering)
- agentic-business-os (governance)

Ensure Supabase branch: orchestration-alignment-v8
Active branch: feature/e2e-orchestration-validation
Apply ADR-036–038 (Supabase DevOps, Telemetry, Alerting)
Phase 7 “Runtime Discovery Persistence” is active

Codex-Frontend: expand schema, add telemetry hooks, validate inserts
Human CLI: confirm DB migrations and remote schema sync
Codex-OS: draft ADRs and task plans for telemetry governance
ABC Agent: maintain continuity between orchestration and persistence testing
Use version LGA-ABC-Rehydration-v1.4.0 as canonical baseline
[END PROMPT]
```

---

## ✅ 12. Summary

This **v1.4.0** rehydration establishes the **Discovery Insights Persistence Phase**, ensuring:

-   Stable schema migration workflow.
-   Expanded telemetry for runtime observability.
-   Future alerting integration for failed AI runs.
-   Dynamic control over AI runtime prompts and model parameters.
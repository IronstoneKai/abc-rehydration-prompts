# 🧠 ABC Rehydration Prompt — LGA Project

**Version:** v1.3.0  
**Date:** 2026-01-28  
**Author:** IronstoneKai  
**Associated Project:** lga-frontend  
**Governance System:** Agentic Business Chain (ABC)  
**Derived From:** Active ABC Session 2026-01-28

---

## 🧩 1. Purpose

This document restores complete context for the Lead Generation Agent (LGA) system and codifies the new **Supabase DevOps discipline (ADR-036)**.  
It ensures that all Codex agents, the ABC Orchestrator, and CLI operators maintain exact schema alignment between GitHub and Supabase, while proceeding toward **E2E orchestration validation** using the Fly.io deployment connected to Supabase branch `orchestration-alignment-v8`.

The primary outcome of this phase is to validate the orchestration layer — verifying that a mock campaign triggers runtime agents that search and process leads across the system in a fully connected environment.

---

## 🧭 2. Canonical Repositories and Artifacts

### **lga-frontend**

| Key Path | Purpose |
|----------|---------|
| `supabase/migrations/` | Active migration files — source of truth for schema |
| `supabase/migrations/20260127225335_remote_schema.sql` | Baseline schema (aligned to Supabase main) |
| `supabase/migrations/20260202000000_orchestration_alignment.sql` | Latest orchestration schema alignment |
| `src/types/database.ts` | Auto-generated Supabase types from current branch |
| `app/api/` | Orchestration, runtime, onboarding, and campaign APIs |
| `.env.local` | Environment toggle (local sandbox vs remote live) |

### **agentic-business-os**

| Key Path | Purpose |
|----------|---------|
| `docs/adr/ADR-027...ADR-036` | Architecture Decision Records, including **ADR-036 DevOps Methodology** |
| `docs/task_plans/` | Active phase tracking and orchestration test plan |

---

## 🧠 3. Agent Roles and Responsibilities

| Agent | Repository | Role | Responsibilities |
|-------|------------|------|------------------|
| **Codex-Frontend** | `lga-frontend` | Engineering | Creates and maintains migration files, Supabase triggers, orchestration APIs |
| **Codex-OS** | `agentic-business-os` | Governance | Updates ADRs and task plans to reflect Supabase/DevOps decisions |
| **ABC Agent** | — | Orchestrator | Validates progress across agents, ensures protocol adherence |
| **Human CLI Operator** | Local | Executor | Runs Supabase CLI and psql validations; verifies schema consistency and live connectivity |

---

## ⚙️ 4. Environment Dichotomy

### 🔹 Local Sandbox (Development)
```env
SUPABASE_DB_URL=postgresql://postgres:postgres@127.0.0.1:54322/postgres
SUPABASE_ENV=local
```

- Used for local testing and reseeding (`supabase db reset`, `supabase db push`)
- **Safe** for destructive operations

### 🔹 Remote Live (E2E Validation)
```env
NEXT_PUBLIC_SUPABASE_URL=https://mohbeouhcaldnpwupims.supabase.co
SUPABASE_DB_URL=postgresql://postgres.czdvkgalxhvvmosbemsg:*****@aws-1-us-east-1.pooler.supabase.com:5432/postgres
SUPABASE_ENV=production
```

- Linked to Supabase branch: `orchestration-alignment-v8`
- Connected to **Fly.io runtime** for orchestration testing
- **Only read and validation queries are allowed** (no resets or pushes)

---

## 🧱 5. Schema Governance Snapshot

### Canonical Table Relationships

| Canonical Table | FK Column | References | Reference Column |
|-----------------|-----------|------------|------------------|
| `accounts` | `id` | — | **PK** |
| `contacts` | `account_id` | `accounts` | `id` |
| `discovery_campaigns` | `account_id` | `accounts` | `id` |
| `discovery_campaigns` | `icp_profile_id` | `icp_profiles` | `id` |
| `icp_profiles` | `user_id` / `updated_by` | `auth.users` | `id` |

✅ **Canonical schema confirmed.**  
All relationships verified against `orchestration-alignment-v8`.  
**ADR-035** and **ADR-036** record these structures and policies.

---

## 🔐 6. Supabase DevOps Enforcement Rules (ADR-036)

1. `/supabase/migrations` in GitHub is the **only source of truth**.
2. All schema edits must be created locally with `supabase migration new`.
3. **Never modify schema directly in Supabase Studio.**
4. Every feature branch must be linked to a Supabase branch before committing.
5. Deprecated migrations are moved to `/supabase/migrations/_deprecated/`.
6. Weekly validation using:
   ```bash
   supabase db diff --linked
   supabase migration list
   ```
7. Before merging to `main`, verify no schema drift exists.

---

## 🧪 7. E2E Orchestration Validation Plan

**Objective:** Validate that a campaign initiated through LGA Frontend triggers the orchestration pipeline and spawns runtime agents correctly in Supabase (via `orchestration-alignment-v8` branch).

### Step 1 — Confirm Branch Connection
```bash
psql "$SUPABASE_DB_URL" -c "SELECT current_database(), current_user, now();"
```
**Expected result:** `czdvkgalxhvvmosbemsg` (Supabase branch reference).

### Step 2 — Verify Schema and Functions
```bash
psql "$SUPABASE_DB_URL" -c "SELECT routine_name FROM information_schema.routines WHERE routine_schema='public';"
```
Ensure all orchestration and runtime functions (e.g., `trigger_runtime_agent`, `compute_insights`) exist.

### Step 3 — Deploy to Fly.io
Ensure the Fly.io machine is linked to this branch’s environment variables.  
Deploy using:
```bash
fly deploy --remote-only
```
Validate `SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY` are set correctly in Fly.io secrets.

### Step 4 — Trigger Campaign
From LGA frontend:
- **Action:** Start a new mock campaign (Discovery Campaign Start).
- **Expected:** Runtime agents spawn and populate telemetry tables in Supabase.

### Step 5 — Verify Orchestration Tables
```bash
psql "$SUPABASE_DB_URL" -c "SELECT count(*) FROM public.orchestration_tasks;"
psql "$SUPABASE_DB_URL" -c "SELECT count(*) FROM public.orchestration_telemetry;"
```
Counts should increase after trigger.

### Step 6 — Log and Record
- **Codex-Frontend** confirms API performance.
- **Human CLI** captures telemetry.
- **Codex-OS** records verification in **ADR-036** and task log.

---

## 🧩 8. Validation Workflow Summary

| Phase | Role | Command | Outcome |
|-------|------|---------|---------|
| Schema validation | Human CLI | `supabase migration list` | Remote == Local |
| Function audit | Human CLI | `SELECT routine_name...` | All orchestration functions present |
| Orchestration trigger | Codex-Frontend | Campaign start via UI | Runtime agents spawned |
| Telemetry check | Human CLI | `SELECT * FROM public.orchestration_telemetry` | New entries recorded |
| Governance log | Codex-OS | ADR-036 update | Confirmation of successful E2E test |

---

## 🧠 9. Phase Context

### ✅ Completed:
- Schema realignment (`orchestration-alignment-v8`)
- Supabase DevOps baseline (ADR-036)

### 🚀 Active Phase:
#### Phase 6 — E2E Live Validation
- Validate orchestration runtime via Fly.io.
- Confirm end-to-end campaign execution and telemetry accuracy.
- Prepare for merge to Supabase `main` after confirmation.

---

## 🪄 10. ABC Bootstrap Snippet

```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (engineering)
- agentic-business-os (governance)

Ensure current Supabase branch: orchestration-alignment-v8
Apply .codexrules.json for agent behavior control
Assume ADR-027–036 are accepted
Phase 6 “E2E Live Validation” is active

Codex-Frontend: run orchestration tests and migrations via IDE
Human CLI: verify via Supabase CLI and Fly.io runtime
ABC Agent: validate logs, interpret results, and authorize merge
Codex-OS: record ADR-036 finalization and orchestration test results
Reference baseline migrations:
  - 20260127225335_remote_schema.sql
  - 20260202000000_orchestration_alignment.sql
Use version LGA-ABC-Rehydration-v1.3.0 as canonical baseline
[END PROMPT]
```

---

## ✅ 11. Summary

This updated rehydration prompt establishes:
- **Stable Supabase DevOps discipline (ADR-036).**
- **Branch-based orchestration testing** via Fly.io linked to `orchestration-alignment-v8`.
- **Full E2E verification protocol** for campaign-driven orchestration.
- **Clear handoff** between Codex-Frontend, Codex-OS, and ABC governance for auditability.

---
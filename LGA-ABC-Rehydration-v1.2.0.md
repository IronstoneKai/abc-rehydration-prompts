# 🧠 ABC Rehydration Prompt — LGA Project

**Version:** v1.2.0  
**Date:** 2026-01-27  
**Author:** IronstoneKai  
**Associated Project:** lga-frontend  
**Governance System:** Agentic Business Chain (ABC)  
**Derived From:** Active ABC Session 2026-01-27

---

## 🧩 1. Purpose

This document restores the full context for the **Lead Generation Agent (LGA)** system under the Agentic Build Chain (ABC).  
It captures the verified local and remote Supabase states, fixes from schema drift, and updates operational protocols so all Codex agents, the ABC Orchestrator, and CLI validations can work together seamlessly toward real-world E2E testing of the live production environment.

---

## 🧭 2. Canonical Repositories and Documents

### **lga-frontend**

| Path | Purpose |
|------|---------|
| `supabase/migrations/` | Active schema migrations (aligned with `accounts.id` as PK) |
| `supabase/schema_baseline.sql` | Locked local verified baseline |
| `supabase/remote_verified_baseline_20260127.sql` | Snapshot of live Supabase schema post-alignment |
| `app/api/` | LGA orchestration, runtime, onboarding, and profile APIs |
| `src/types/database.ts` | Generated Supabase TypeScript types (post realignment) |
| `docs/sandbox_seed_phaseX.sql` | Latest reseed file used for sandbox resets |
| `.env.local` | Dual-environment configuration file (see §4) |

### **agentic-business-os**

| Path | Purpose |
|------|---------|
| `docs/adr/ADR-027...ADR-035` | Official decision history (ADR-035: `accounts.id` promotion) |
| `docs/task_plans/` | Ongoing project phase tracking |
| `docs/task_plans/README.md` | Phase summary table and status index |

---

## 🧠 3. Agent Roles and Protocols

| Agent | Role | Repository | Responsibilities |
|-------|------|------------|------------------|
| **Codex-Frontend** | Engineering | `lga-frontend` | Writes and modifies SQL migrations, Supabase triggers, API handlers |
| **Codex-OS** | Governance | `agentic-business-os` | Updates ADRs and Task Plans to record architectural state |
| **ABC Agent** | Orchestrator (you) | — | Coordinates development between agents and human CLI validation |
| **Human CLI Operator** | Executor | Local machine | Runs commands, verifies schema, and validates Supabase connectivity |

### Workflow Logic

1. **Codex-Frontend** edits → creates migration or code file.
2. **Human** executes and validates via CLI.
3. **ABC Agent** interprets terminal results and authorizes continuation.
4. **Codex-OS** updates ADR and governance records.

> This cycle continues until live E2E validation completes.

---

## ⚙️ 4. Environment Dichotomy and Validation Rules

Your `.env.local` file supports two operational modes within the same workspace:

### 🔹 Local Sandbox (Development)
```env
SUPABASE_DB_URL=postgresql://postgres:postgres@127.0.0.1:54322/postgres
SUPABASE_ENV=local
```

**Usage:**
- For testing migrations and reseeds locally before promoting to live.
- Uses Dockerized Supabase and `supabase db reset`.

### 🔹 Remote Production (Live)
```env
NEXT_PUBLIC_SUPABASE_URL=https://mohbeouhcaldnpwupims.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...
SUPABASE_DB_URL=postgresql://postgres.mohbeouhcaldnpwupims:Zu6...@aws-1-us-east-1.pooler.supabase.com:5432/postgres
SUPABASE_ENV=production
```

**Usage:**
- For end-to-end verification and customer-experience testing.
- Must **never** apply destructive diffs or `supabase db reset`.
- All validation is **read-only** via CLI queries.

---

## 🧱 5. Schema Governance Snapshot

### ✅ Canonical Schema: `accounts.id` as PK

| Table | FK Column | References | Reference Column |
|-------|-----------|------------|------------------|
| `contacts` | `account_id` | `accounts` | `id` |
| `discovery_campaigns` | `account_id` | `accounts` | `id` |
| `discovery_campaigns` | `icp_profile_id` | `icp_profiles` | `id` |
| `icp_profiles` | `user_id` / `updated_by` | `auth.users` | `id` |

- All local and remote tables identical.
- No legacy `account_id` field exists in `accounts`.
- **ADR-035** records this architectural promotion.

---

## 🔐 6. Schema Lock Procedure

1. **Dump local verified baseline**
   ```bash
   supabase db dump --local --schema public --file supabase/schema_baseline.sql
   ```

2. **Dump live verified baseline**
   ```bash
   supabase db dump --linked --schema public --file supabase/remote_verified_baseline_$(date +%Y%m%d).sql
   ```

3. **Commit and tag**
   ```bash
   git add supabase/schema_baseline.sql supabase/remote_verified_baseline_20260127.sql
   git commit -m "Lock schema baselines (local + remote parity)"
   git push origin feature/supabase-ssr-integration
   ```

---

## 🔁 7. CLI Validation Workflow

### 🔹 Local Sandbox Validation
```bash
supabase start
supabase db reset
psql "$SUPABASE_DB_URL" -c "select count(*) from public.accounts;"
```

### 🔹 Remote Live Validation
```bash
psql "$SUPABASE_DB_URL" -c "select table_name from information_schema.tables where table_schema='public';"
psql "$SUPABASE_DB_URL" -c "select count(*) from public.orchestration_tasks;"
```

> [!CAUTION]
> Never run `supabase db reset` or `supabase db push` against remote.  
> The live DB must be validated only via linked diffs and read queries.

---

## 📚 8. ADR and Canonical References

| ADR | Title | Purpose |
|-----|-------|---------|
| **ADR-027** | Foundational DiscoveryAgent Schema | Defines discovery and orchestration structure |
| **ADR-030** | Event-Driven Orchestration | Introduces runtime trigger automation |
| **ADR-035** | `accounts.id` PK Promotion | Establishes canonical account key for CRM integration |
| **ADR-036** | Schema Baseline Lock | Defines dual-environment parity protocol (local vs live) |

> Codex-OS must keep these updated and push changes to `agentic-business-os/docs/adr/`.

---

## 🧪 9. Phase Context

### ✅ Completed
| Phase | Description | ADR | Status |
|-------|-------------|-----|--------|
| 1–4 | DiscoveryAgent + Runtime + Event Pipeline | ADR-027–030 | Complete |
| 5 | Schema Alignment (`accounts.id`) | ADR-035 | Complete |

### 🚀 Active

#### Phase 6 — E2E Live Validation

- **Purpose**: Verify orchestration pipeline from remote Fly.io app through Supabase (live)
- **Codex-Frontend**: ensures runtime orchestration endpoints behave identically in live mode
- **Human CLI**: validates telemetry + orchestration flow remotely
- **Codex-OS**: records results and governance compliance in ADR-036

---

## 🧩 10. E2E Validation Sequence

1. **Switch to remote credentials**
   ```bash
   # In .env.local
   # Comment out local DB_URL
   # Uncomment SUPABASE_DB_URL for production
   ```

2. **Verify connectivity**
   ```bash
   psql "$SUPABASE_DB_URL" -c "select current_database(), current_user, now();"
   ```

3. **Verify orchestration consistency**
   ```bash
   psql "$SUPABASE_DB_URL" -c "select count(*) from public.orchestration_tasks;"
   psql "$SUPABASE_DB_URL" -c "select count(*) from public.orchestration_telemetry;"
   ```

4. **Trigger a sample orchestration run via Fly.io app**
   - Execute a “discovery campaign start” through the LGA frontend.
   - Observe telemetry updates.

5. **Record verification output**
   - Send results back to **ABC Agent** for interpretation and approval.

---

## 🪄 11. ABC Bootstrap Snippet

```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (engineering)
- agentic-business-os (governance)
Apply .codexrules.json enforcement for Codex agents.
Assume phases ≤5 are complete, Phase 6 “E2E Live Validation” is active.
Use environment dichotomy (.env.local) to toggle sandbox vs remote.
Codex-Frontend edits and commits; human validates via CLI; ABC Agent authorizes; Codex-OS records in ADR.
Reference: schema_baseline.sql (local), remote_verified_baseline_20260127.sql (remote).
Use version LGA-ABC-Rehydration-v1.2.0 as canonical baseline.
[END PROMPT]
```

---

## ✅ 12. Summary

This new rehydration file establishes:
- **Full schema parity** between sandbox and live environments.
- **Verified Supabase connectivity** for production testing.
- **ABC agent orchestration protocol** for safe live E2E validation.
- **Governance link** to ADR-035/036 for long-term schema traceability.

When you start a new conversation, paste this entire file (or just the `[BEGIN ... END PROMPT]` block).  
You’ll resume exactly where we left off — ready to execute live E2E validation for the LGA system.

---

**End of Document — LGA-ABC-Rehydration-v1.2.0.md**
# 🧠 ABC Rehydration Prompt — LGA Project

**Version:** v1.4.0  
**Date:** 2026-01-30  
**Author:** IronstoneKai  
**Associated Project:** lga-frontend  
**Governance System:** Agentic Business Chain (ABC)  
**Derived From:** Active ABC Session — E2E Orchestration Validation (2026-01-30)

---

## 🧩 1. Purpose

Rehydrate full project context for Lead Generation Assistant (LGA) following schema extensions to `discovery_insights` and `discovery_leads`.  
This phase validates that runtime agents now populate verifiable company and lead data and that Codex-Frontend and Codex-OS remain synchronized under **ADR-036 Supabase DevOps Methodology**.

---

## 📁 2. Canonical Repositories

| Repository | Role | Key Areas |
|------------|------|-----------|
| **lga-frontend** | Engineering | `/supabase/migrations`, `/src/app/api/discovery`, `/src/types/database.ts` |
| **agentic-business-os** | Governance | `/docs/adr/ADR-027–036`, `/docs/business/LGA_Business_Model.md`, `/docs/supabase/Schema_Migration_Process.md` |

**Linked Supabase branch:** `orchestration-alignment-v8`  
**Active Fly.io app:** `lga-runtime`

---

## 🧠 3. Current Phase Context

### ✅ Completed
- Schema extended to support company metadata and verified leads
- Supabase types regenerated and deployed
- Runtime orchestration functioning end-to-end
- Business model defined (multi-tenant SaaS with ICP-based discovery)

### 🚀 Active
#### Phase 7 — Data Quality & Lead Verification
- Transition from mock to verifiable research data
- Implement runtime enrichment logic for company + lead verification
- Extend discovery results from 3 → 5 leads per campaign

---

## 🧱 4. Schema Snapshot (Post-Migration)

### Tables
- **`discovery_insights`**: now includes company website, type, employee count, location, source URLs, and LinkedIn.
- **`discovery_leads`**: contains company name, lead name/title/email/LinkedIn, confidence score, verification fields, and FK → `discovery_insights.id`.

### Relations
| Child Table | FK Column | Parent Table | Reference |
|-------------|-----------|--------------|-----------|
| `discovery_leads` | `insight_id` | `discovery_insights` | `id` |

---

## ⚙️ 5. Agent Roles

| Agent | Role | Responsibilities |
|-------|------|------------------|
| **Codex-Frontend** | Engineering | Update runtime logic and UI to populate new discovery fields; extend data volume; enforce Supabase DevOps discipline. |
| **Codex-OS** | Governance | Update business and data governance docs (LGA Business Model v1.0); record Phase 7 learnings in ADR-036. |
| **Human CLI Operator** | Executor | Validate Supabase migrations, Fly.io deployment, and runtime logs. |
| **ABC Agent** | Orchestrator | Maintain session continuity, coordinate validation, and confirm data provenance. |

---

## 🔐 6. Supabase DevOps Rules (ADR-036 Summary)

1. GitHub `/supabase/migrations` = single source of truth
2. Never modify schema in Supabase Studio
3. Each branch must link to a Supabase branch before commit
4. Push using `supabase db push --linked`
5. Validate via `supabase migration list --linked`
6. Regenerate types:
   ```bash
   supabase gen types typescript --linked > src/types/database.ts
   ```
7. Only deploy verified migrations through Fly.io

---

## 🧪 7. Verification & Testing Plan

### Trigger new discovery campaign via UI
→ runtime agents run real-world research

### Inspect inserted records
```sql
SELECT * FROM discovery_insights ORDER BY created_at DESC LIMIT 5;
SELECT * FROM discovery_leads ORDER BY created_at DESC LIMIT 5;
```

- Confirm enrichment fields (website, employees, LinkedIn, verified flag) populated.
- Validate quantity — 5 unique leads per campaign.
- Fly.io logs show real-time AI runtime research output.
- Codex-Frontend refines runtime pipeline and lead verification heuristics.

---

## 🧩 8. Business Model Alignment

Refer to `/docs/business/LGA_Business_Model.md` for:
- Multi-tenant design (tenant → campaign → insight → lead)
- Verification Layer for credibility
- Token accounting per discovery run
- Usage-based pricing linkage

---

## 🪄 9. ABC Bootstrap Snippet

```text
[BEGIN ABC REHYDRATION PROMPT]
Load canonical repositories:
- lga-frontend (engineering)
- agentic-business-os (governance)

Ensure Supabase branch: orchestration-alignment-v8  
Phase 7 “Data Quality & Lead Verification” active  
ADR-027–036 accepted  
Codex-Frontend: implement runtime enrichment logic and expand lead count to 5  
Codex-OS: update business model and ADR-036 validation log  
Human CLI: validate schema and Fly.io deployment  
ABC Agent: maintain session state and authorize merge to main after verification
Use version LGA-ABC-Rehydration-v1.4.0 as canonical baseline
[END PROMPT]
```

---

## ✅ 10. Summary

This prompt rehydrates full operational context for Phase 7, ensuring:
- **Stable schema** with verified company + lead structure
- **Supabase DevOps discipline** remains intact
- **Codex-Frontend** can safely deploy enrichment logic and data validation
- **ABC system** maintains continuity for governance, orchestration, and runtime testing
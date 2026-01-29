# Changelog — LGA ABC Rehydration Prompts

All notable changes to the rehydration prompts will be documented in this file.

## [v1.1.0] — 2026-01-25
### Added
- **Interactive CLI Validation Protocol**: A formal 5-step protocol for human-in-the-loop validation, ensuring dynamic execution feedback between the ABC Agent and the operator.
- **Canonical Database Reset Procedure**: Defines the mandatory `npm run db:reset` command and verification checks to ensure sandbox health before validation.
- **Simplified Local Sandbox Reset**: Integrated `npm run db:reset` as the canonical entrypoint for CLI validation, automating Docker startup, migrations, and reseeding.
- **Strategic Summary**: Added a comparison table for the workflow improvements.

## [v1.2.0] — 2026-01-27
### Added
- **Production Environment Integration**: Dual-mode `.env.local` support for toggling between local sandbox and remote live production.
- **Unified Schema Governance**: Protocols for locking local and remote schema baselines (ADR-036).
- **Live E2E Validation**: Formal checklist for verifying remote connectivity and orchestration telemetery safely.
- **Updated Canonical References**: Added `remote_verified_baseline_20260127.sql` and `ADR-035` promotion.

## [v1.3.0] — 2026-01-28
### Added
- **Supabase DevOps Discipline (ADR-036)**: Establishes strict rules for schema management, ensuring GitHub is the single source of truth.
- **Branch-Based Development**: Linked validation to Supabase branch `orchestration-alignment-v8`.
- **E2E Orchestration Validation**: Comprehensive 6-step protocol for validating runtime agent triggering via Fly.io.
- **Updated Environment Dichotomy**: Clarified roles of local sandbox vs. remote live environment with branch connectivity.

## [v1.4.0] — 2026-01-29
### Added
- **Discovery Insights Persistence**: Expanded schema support for AI outputs (`summary`, `details`, `model`) via `public.discovery_insights`.
- **Runtime Telemetry & Alerting (ADR-038)**: New telemetry events for runtime execution and failure tracking.
- **Dynamic Configuration**: Introduced `public.runtime_configs` for managing model versions and prompts dynamically.
- **Phase 7 Context**: Active support for "Runtime Discovery Persistence" phase.

### 🧭 Strategic Summary
| Category | Old Workflow | Improved Workflow |
|-----------|---------------|-------------------|
| Sandbox startup | Manual `docker compose up` | Automated within `npm run db:reset` |
| Reset sequence | Multi-step (compose + db reset + seed) | One command |
| Documentation | Implicit | Explicit in v1.1.0 rehydration |
| Benefit | None | Simplifies CLI validation, eliminates drift |

---
### Changed
- Updated canonical version reference in the [BEGIN ABC REHYDRATION PROMPT] block to `v1.1.0`.

---

## [v1.0.0] — 2026-01-25
### Initial Release
- Rehydrates context for the LGA (Lead Generation Agent) program.
- Defines canonical repositories: `lga-frontend` and `agentic-business-os`.
- Establishes agent roles: Codex-Frontend (Engineering), Codex-OS (Governance), and ABC Agent (Orchestration).
- Includes CLI validation workflows and bootstrapping instructions.

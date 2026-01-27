# Changelog — LGA ABC Rehydration Prompts

All notable changes to the rehydration prompts will be documented in this file.

## [v1.1.0] — 2026-01-25
### Added
- **Interactive CLI Validation Protocol**: A formal 5-step protocol for human-in-the-loop validation, ensuring dynamic execution feedback between the ABC Agent and the operator.
- **Canonical Database Reset Procedure**: Defines the mandatory `npm run db:reset` command and verification checks to ensure sandbox health before validation.
- **Simplified Local Sandbox Reset**: Integrated `npm run db:reset` as the canonical entrypoint for CLI validation, automating Docker startup, migrations, and reseeding.
- **Strategic Summary**: Added a comparison table for the workflow improvements.

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

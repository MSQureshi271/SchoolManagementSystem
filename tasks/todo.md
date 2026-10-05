# School Management System — MVP task checklist

**Prepared:** 5 October 2026. **Status:** all implementation tasks pending.
**Companion:** [plan.md](plan.md) defines scope, business rules, architecture and release gates.

## How to execute this checklist

There are **119 tasks in 12 phases**. IDs remain stable; suffixed tasks make prerequisites explicit without renumbering later work. The list is in dependency order. All paths are proposed repository-relative paths; timestamp placeholders represent one actual migration directory per task. No application files or commands currently exist.

1. Read the task's requirement IDs and the corresponding plan section/module spec; resolve decisions whose deadline has arrived.
2. Check direct dependencies and the last checkpoint evidence. Claim an owner; choose one task, not the whole phase.
3. Define transport/schema changes before consumers. For behavioural changes, write a meaningful failing test first; implement the smallest usable slice.
4. Complete the listed acceptance and verification, plus the standing Definition of Done. Record actual commands/results in the PR or `docs/verification/<task-id>.md`. Do not check off a task based on compilation alone.
5. Review security, correctness, architecture and scope; commit the focused slice on a short-lived `codex/<task-id>-<name>` branch. Merge/deploy under the repository's applicable review and authorization rules.
6. If a task exceeds five principal hand-edited files or one focused session, split it into stable suffixed subtasks before implementation. Listed files include the main implementation/test targets; generated files, manifest wiring and lockfile changes must still be reviewed.
7. Stop dependent work when checks fail. Keep unrelated completed work; diagnose and add a regression test. Update the spec/plan when a decision changes.
8. Checkpoints occur every two or three tasks. Show the integrated result for review; do not turn minor routine checks into repeated permission requests. Scope/policy/release decisions remain explicit.

**Ownership:** P00 product/technical lead with school representative; P01/P02 backend/platform and client owners; P03–P08 feature owner plus the other platform owner; P09 QA/security with feature owners; P10/P11 release/support owner with school acceptance. One owner serializes database migrations and shared contracts. Pair independent client work only after its provider contract is stable.

**Size:** S = roughly 1–2 principal files, M = 3–5. Session-sized tasks may still need additional investigation; phase engineer-day ranges in plan.md are estimates, not sums of guaranteed task durations. The eight suffixed tasks are included within the phase range and must be included when re-estimating after P01.

## Verification profiles

These are **future script contracts established during P01**, not tests already run. Every task's specific scenario below is mandatory in addition to its profile. Run only checks affected by a change; run the phase integration gate before advancing. A task requiring devices or provider credentials cannot be marked verified merely because mocks pass.

| Code | Required verification |
|---|---|
| DOC | Review requirements, decisions, references, task dependencies and examples; no runtime test claim for prose. |
| BOOT | Safe first install, reviewed script allowlist, lockfile and clean-install verification; record pinned versions. |
| C | `pnpm lint`, `pnpm format:check`, `pnpm typecheck`, `pnpm build` for affected code. |
| U | `pnpm test:unit`; use the owning package's focused Vitest test during red/green iteration. |
| DB | `pnpm db:generate`, disposable `pnpm db:migrate:deploy`, schema/constraint tests and previous-schema fixture upgrade. |
| I | `pnpm test:integration` against isolated real Postgres plus affected unit tests; use real auth/policies. |
| W | Affected web component tests and `pnpm test:e2e:web`; inspect browser console/network/layout/keyboard behaviour. |
| N | `pnpm test:mobile`, `pnpm mobile:check`, relevant `pnpm test:e2e:mobile`; installed Android/iOS builds and real hardware where required. |
| LOAD | `pnpm test:load` against synthetic staging; fixed dataset/workload/network, latency distribution, error/integrity counts and before/after evidence. |
| OPS | Use the named runbook on staging/isolated restore first; verify telemetry, provider results and actual recovery outcome. Production actions need the release owner's authorization. |

A listed end-to-end file/route may be introduced at the task that owns it. Earlier slices can be verified through focused integration and manual clients until the corresponding E2E harness lands. P01 bootstrap tasks record available checks without claiming nonexistent suites pass.

## Roadmap requirements covered

R01 identity; R02 school data; R03 role dashboards; R04 attendance marking; R05 attendance views/reports; R06 timetable; R07 notices/files; R08 homework/submission/grading; R09 inbox/push; R10 offline reading; R11 network resilience; R12 secure, accessible and operable release. Full acceptance definitions are in plan.md §2.

## P00 — Product contracts and design

**Exit outcome:** A reviewable scope, capability map, policy baseline and screen design exist before feature work.

### T001 — Record the MVP boundary

- [ ] **Complete T001**
- **Dependencies:** None. **Requirements:** R01–R12. **Estimated scope:** M.
- **Files:** `docs/decisions/0001-mvp-scope.md`, `docs/capability-map.md`, `tasks/plan.md`.
- **Acceptance:** Separate pilot/public release from deferred roadmap modules; record the agreed client/role matrix and Q01/Q02/Q18 decisions.
- **Verify (DOC):** Review every R01–R12 row against ROADMAP.md; unresolved choices retain owner and decision deadline.

### T002 — Record stack compatibility decisions

- [ ] **Complete T002**
- **Dependencies:** T001. **Requirements:** R11,R12. **Estimated scope:** S.
- **Files:** `docs/decisions/0002-stack.md`, `docs/dependencies.md`.
- **Acceptance:** Record chosen versions, official sources, alternatives, support windows and unverified integrations; assign P01 proof criteria.
- **Verify (DOC):** Review Expo/React/Node/Prisma/auth compatibility and library license assumptions; no floating CLI instructions.

### T003 — Specify identity and permissions

- [ ] **Complete T003**
- **Dependencies:** T001. **Requirements:** R01,R02,R12. **Estimated scope:** M.
- **Files:** `docs/specs/identity.md`, `docs/specs/school-access.md`, `docs/permissions.md`.
- **Acceptance:** Define invite/account/student distinctions, multiple memberships/roles, last-admin rule and the allow/deny matrix; list Q04/Q05 blockers.
- **Verify (DOC):** Walk an admin, a teacher-parent and an unrelated parent through each permission; review revoked memberships and cross-school IDs.

**Checkpoint P00.1 — through T003**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T004 — Specify academic business rules

- [ ] **Complete T004**
- **Dependencies:** T001,T003. **Requirements:** R02–R08. **Estimated scope:** M.
- **Files:** `docs/specs/academics.md`, `docs/specs/attendance.md`, `docs/specs/timetable.md`, `docs/specs/assignments.md`, `docs/specs/notices.md`.
- **Acceptance:** Define enrolment dates, attendance denominator, publication/submission states and audience history; preserve all unresolved policy questions.
- **Verify (DOC):** Use concrete transfer, holiday, overdue-work and grade-correction examples; each R02–R08 behaviour has acceptance scenarios.

### T005 — Design the principal user journeys

- [ ] **Complete T005**
- **Dependencies:** T003,T004. **Requirements:** R01–R11. **Estimated scope:** M.
- **Files:** `docs/design/flows.md`, `docs/design/screen-inventory.md`, `docs/design/content.md`, `packages/design-tokens/src/index.ts`.
- **Acceptance:** Wireframe setup, register, timetable, notice, homework and child dashboard; cover web/native navigation and non-happy states.
- **Verify (DOC):** Review with realistic long names and 320px layout; annotate keyboard/focus, child context, offline and conflict states.

### T006 — Map threats and data lifecycle

- [ ] **Complete T006**
- **Dependencies:** T003,T004. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `docs/data-model.md`, `docs/privacy/data-inventory.md`, `docs/privacy/retention.md`, `docs/decisions/0003-tenancy.md`, `docs/decisions/0004-auth.md`.
- **Acceptance:** Map every trust boundary and personal field to purpose/access/retention; record tenancy/auth choices and unapproved policy items.
- **Verify (DOC):** Trace cross-school access, malicious upload, revoked guardian, shared device and restored-backup deletion scenarios.

**Checkpoint P00.2 — through T006**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P00 exit gate:** A reviewable scope, capability map, policy baseline and screen design exist before feature work. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P01 — Workspace and walking skeleton

**Exit outcome:** Fresh checkout builds; one browser and native client can reach a tested API/database/auth skeleton.

### T007 — Create the workspace and safe install policy

- [ ] **Complete T007**
- **Dependencies:** T002. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `package.json`, `pnpm-workspace.yaml`, `.gitignore`, `.node-version`, `.env.example`.
- **Acceptance:** Pin runtime/manager, declare apps/packages and script policy; exclude secrets/build output and supply placeholder configuration.
- **Verify (BOOT):** Bootstrap with dependency scripts disabled; inspect required scripts before narrow approval; create one reviewed lockfile.

### T008 — Establish compiler and quality conventions

- [ ] **Complete T008**
- **Dependencies:** T007. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `tsconfig.base.json`, `eslint.config.mjs`, `.prettierrc.json`, `scripts/check-boundaries.ts`, `CONTRIBUTING.md`.
- **Acceptance:** Strict types and formatting are shared; client imports of database/server modules fail lint; commands are PowerShell/Linux compatible.
- **Verify (C):** Run static checks on sample valid/invalid boundaries; verify no blanket any/ignore rules.

### T009 — Start local infrastructure

- [ ] **Complete T009**
- **Dependencies:** T007. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `infra/compose.yaml`, `packages/database/package.json`, `packages/database/prisma.config.ts`, `packages/database/prisma/schema.prisma`, `packages/database/src/client.ts`.
- **Acceptance:** Local Postgres, private object service, mail catcher and scanner start with health checks; Prisma v7 generates and queries.
- **Verify (DB):** Run compose up, db:generate and a disposable migration/query; verify no production credentials or exposed public bucket.

**Checkpoint P01.1 — through T009**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T010 — Build the API skeleton

- [ ] **Complete T010**
- **Dependencies:** T008,T009. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `apps/api/package.json`, `apps/api/tsconfig.json`, `apps/api/src/app.ts`, `apps/api/src/server.ts`, `apps/api/src/config/env.ts`.
- **Acceptance:** ESM API starts, validates config, exposes minimal health routes and shuts down connections cleanly.
- **Verify (C):** Build/start server; check liveness/readiness success and missing-config/database-unavailable failure behaviour.

### T011 — Build the web shell

- [ ] **Complete T011**
- **Dependencies:** T005,T008,T010. **Requirements:** R03,R11. **Estimated scope:** M.
- **Files:** `apps/web/package.json`, `apps/web/vite.config.ts`, `apps/web/src/main.tsx`, `apps/web/src/app/router.tsx`, `apps/web/src/app/shell.tsx`.
- **Acceptance:** Vite/React shell routes correctly, proxies API locally and has loading/error/not-found states.
- **Verify (W):** Build and open portal; reload a nested route and check health request, mobile layout and clean console.

### T011a — Build reusable accessible web controls

- [ ] **Complete T011a**
- **Dependencies:** T011,T005. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `apps/web/src/components/Button.tsx`, `apps/web/src/components/Field.tsx`, `apps/web/src/components/Dialog.tsx`, `apps/web/src/components/Table.tsx`, `apps/web/src/components/Status.tsx`.
- **Acceptance:** Shared controls use semantic tokens, labels, focus handling and loading/error/empty states; table remains usable at narrow widths.
- **Verify (W):** Keyboard/screen-reader review in shell showcase; add focused behaviour tests with consuming features instead of snapshot-only tests.

**Checkpoint P01.2 — through T011a**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T012 — Build the native shell

- [ ] **Complete T012**
- **Dependencies:** T005,T008,T010. **Requirements:** R03,R11,R12. **Estimated scope:** M.
- **Files:** `apps/mobile/package.json`, `apps/mobile/app.config.ts`, `apps/mobile/eas.json`, `apps/mobile/src/app/_layout.tsx`, `apps/mobile/src/app/index.tsx`.
- **Acceptance:** SDK-matched Expo app opens on Android/iOS with separate environment identifiers and safe-area/error handling.
- **Verify (N):** Run mobile:check; build/install development apps on Android and iOS; call staging/local API over configured networking.

### T012a — Build native UI primitives

- [ ] **Complete T012a**
- **Dependencies:** T012,T005. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `apps/mobile/src/components/Button.tsx`, `apps/mobile/src/components/Field.tsx`, `apps/mobile/src/components/Screen.tsx`, `apps/mobile/src/components/Status.tsx`, `apps/mobile/src/components/ErrorBoundary.tsx`.
- **Acceptance:** Native controls support dynamic text, screen-reader labels, keyboard avoidance and useful errors with shared tokens.
- **Verify (N):** View on both development builds; exercise large text, TalkBack/VoiceOver, loading and controlled error recovery.

### T013 — Create shared API contracts and client

- [ ] **Complete T013**
- **Dependencies:** T010,T011,T012. **Requirements:** R11. **Estimated scope:** M.
- **Files:** `packages/contracts/src/common.ts`, `packages/api-client/src/client.ts`, `packages/api-client/src/errors.ts`, `scripts/export-openapi.ts`, `packages/api-client/src/client.test.ts`.
- **Acceptance:** Define envelopes, pagination, timestamps, version conflict and transport injection; unsafe mutation retries are disabled.
- **Verify (U):** Unit-test error normalization and cancellation; validate one API response and emit OpenAPI without Node-only client imports.

**Checkpoint P01.3 — through T013**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T014 — Install the test harness and synthetic fixtures

- [ ] **Complete T014**
- **Dependencies:** T009,T013. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `apps/api/vitest.config.ts`, `apps/api/tests/helpers/database.ts`, `packages/test-fixtures/src/index.ts`, `apps/web/vitest.config.ts`, `apps/mobile/jest.config.cjs`.
- **Acceptance:** Isolated real-DB integration, web component and native component runners work; two schools/four roles are synthetic.
- **Verify (I):** Run unit/integration/mobile smoke tests twice with fresh fixture setup to prove isolation, not as reassurance after identical runs.

### T015 — Prove the authentication stack integration

- [ ] **Complete T015**
- **Dependencies:** T010,T012,T014. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/auth/auth.ts`, `apps/web/src/lib/auth-client.ts`, `apps/mobile/src/lib/auth-client.ts`, `apps/api/tests/integration/auth-stack.test.ts`, `docs/dependencies.md`.
- **Acceptance:** Pinned Better Auth/Prisma/Express works with SDK57; web cookie and native SecureStore session both access a protected probe.
- **Verify (I+N):** Exercise sign-in, session restore and revocation on both clients; verify parser ordering and one database schema authority; record actual versions.

### T016 — Establish logs and request diagnostics

- [ ] **Complete T016**
- **Dependencies:** T010,T013. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/platform/logger.ts`, `apps/api/src/platform/telemetry.ts`, `apps/api/src/middleware/errors.ts`, `apps/api/src/middleware/request-id.ts`, `apps/api/tests/integration/errors.test.ts`.
- **Acceptance:** Structured errors/request IDs and startup telemetry exist; sensitive payloads and route tokens are redacted.
- **Verify (I):** Force a validation error and internal exception; find correlated sanitized logs and test generic 500 output.

**Checkpoint P01.4 — through T016**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T017 — Add reproducible build and CI checks

- [ ] **Complete T017**
- **Dependencies:** T008,T014,T015,T016. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `package.json`, `.github/workflows/ci.yml`, `.github/dependabot.yml`, `infra/Dockerfile`, `docs/runbooks/deploy.md`.
- **Acceptance:** CI uses frozen approved installs and gates static checks/tests/contracts/build/audit; web/API image is reproducible.
- **Verify (C+I):** Run documented root commands and build image; intentionally failing disposable check proves merge gate; inspect CI secret permissions.

### T017a — Establish browser and device E2E runners

- [ ] **Complete T017a**
- **Dependencies:** T014,T017. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `playwright.config.ts`, `tests/e2e/web/smoke.spec.ts`, `tests/e2e/mobile/smoke.yaml`, `scripts/test-mobile.ts`, `package.json`.
- **Acceptance:** Root E2E scripts run pinned Playwright/Maestro against configured test services; artifacts exclude secrets/pupil data.
- **Verify (W+N):** Run empty-shell web/native smoke and force a known failure to verify reports and nonzero exit; document device prerequisites.

### T018 — Document and verify first-run setup

- [ ] **Complete T018**
- **Dependencies:** T011,T012,T017,T017a,T011a,T012a. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `README.md`, `AGENTS.md`, `docs/verification/T018.md`, `packages/database/prisma/seed.ts`.
- **Acceptance:** Fresh checkout instructions cover Windows/Linux, native/iOS prerequisites and synthetic seed; no required step relies on undocumented global tooling.
- **Verify (C+N):** Follow README on a clean environment; demonstrate browser/native→API→DB and record unresolved compatibility limits before P02.

**Checkpoint P01.5 — through T018**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T018a — Provision synthetic HTTPS staging early

- [ ] **Complete T018a**
- **Dependencies:** T018. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `infra/deploy.yaml`, `infra/reverse-proxy.conf`, `.github/workflows/staging.yml`, `docs/runbooks/deploy.md`, `docs/verification/T018a.md`.
- **Acceptance:** Approved staging services provide HTTPS, isolated Postgres/private storage/mail restrictions, backups and deployment of tested candidate; use synthetic data only and record region/cost/account decisions.
- **Verify (OPS):** Deploy walking skeleton, check HTTPS session transport/readiness, perform sample backup and verify private object access; this environment hosts all later staging/load/recovery checks.

**Checkpoint P01.6 — through T018a**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P01 exit gate:** Fresh checkout builds; one browser and native client can reach a tested API/database/auth skeleton. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P02 — Identity, invitations and school access

**Exit outcome:** Every role can authenticate; account status and tenant/relationship checks are server enforced.

### T019 — Introduce schools and multi-role memberships

- [ ] **Complete T019**
- **Dependencies:** T006,T018,T018a. **Requirements:** R01,R02,R12. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_school_access/migration.sql`, `packages/contracts/src/identity.ts`, `apps/api/tests/integration/school-access.test.ts`.
- **Acceptance:** Add school, membership and role constraints with school status/timezone; duplicate roles/memberships are rejected.
- **Verify (DB+I):** Apply migration to empty/previous schema; test cross-school references and duplicate membership constraints.

### T019a — Create transactional mutation infrastructure

- [ ] **Complete T019a**
- **Dependencies:** T019,T018. **Requirements:** R04,R09,R12. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_mutation_records/migration.sql`, `apps/api/src/platform/idempotency.ts`, `apps/api/src/platform/audit.ts`, `apps/api/tests/integration/idempotency.test.ts`.
- **Acceptance:** Add audit/outbox/idempotency storage; canonical request hash and atomic key claim/result commit prevent retry duplicates.
- **Verify (I):** Race equal keys and unequal payloads; inject failure before/after commit; recheck permission before replay; verify 30-day retention contract.

### T020 — Add operator bootstrap and invitation records

- [ ] **Complete T020**
- **Dependencies:** T019,T019a. **Requirements:** R01,R02. **Estimated scope:** M.
- **Files:** `scripts/bootstrap-school.ts`, `apps/api/src/modules/identity/invitations.service.ts`, `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_invitations/migration.sql`, `apps/api/tests/integration/invitations.test.ts`.
- **Acceptance:** Bootstrap creates school plus first-admin invitation; tokens are digest-stored, expiring, single-use and revocable.
- **Verify (I):** Create/retry bootstrap against synthetic DB; test expiry, replay, wrong email and no self-assigned admin role.

**Checkpoint P02.1 — through T020**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T021 — Gate account creation and accept invitations

- [ ] **Complete T021**
- **Dependencies:** T020,T015. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/auth/invitation-gate.ts`, `apps/api/src/modules/identity/routes.ts`, `apps/api/src/modules/identity/invitations.service.ts`, `apps/api/tests/integration/invitation-acceptance.test.ts`, `packages/contracts/src/identity.ts`.
- **Acceptance:** No uninvited public signup grants access; acceptance applies server-held roles and preserves existing memberships; partial failures are recoverable.
- **Verify (I):** Test concurrent acceptance, direct auth endpoint bypass, revoked invitation and existing-user second-school join.

### T022 — Wire account email delivery and verification

- [ ] **Complete T022**
- **Dependencies:** T021. **Requirements:** R01. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/identity/email.ts`, `apps/api/src/auth/auth.ts`, `apps/api/src/jobs/send-email.ts`, `apps/api/tests/integration/email-verification.test.ts`, `docs/specs/identity.md`.
- **Acceptance:** OTP and reset links use expiring single-use library flows with rate limits; delivery failures are visible and no secrets enter logs.
- **Verify (I):** Use mail catcher to verify OTP expiry/attempt/resend limits, invalid code, generic public responses and reset-link replay.

### T023 — Build web account entry flows

- [ ] **Complete T023**
- **Dependencies:** T022,T011. **Requirements:** R01. **Estimated scope:** M.
- **Files:** `apps/web/src/features/identity/pages/SignInPage.tsx`, `apps/web/src/features/identity/pages/AcceptInvitePage.tsx`, `apps/web/src/features/identity/pages/VerifyEmailPage.tsx`, `apps/web/src/features/identity/pages/ResetPasswordPage.tsx`, `apps/web/src/features/identity/identity.test.tsx`.
- **Acceptance:** Invited user signs in/verifies/recovers with accessible errors; failed network does not clear entered email or reveal account existence.
- **Verify (W):** Test forms and complete invitation→OTP→login→forgot/reset on real web/API; confirm HttpOnly Secure cookie in HTTPS staging configuration.

**Checkpoint P02.2 — through T023**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T024 — Build native account entry flows

- [ ] **Complete T024**
- **Dependencies:** T022,T012. **Requirements:** R01. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/identity/screens/SignInScreen.tsx`, `apps/mobile/src/features/identity/screens/AcceptInviteScreen.tsx`, `apps/mobile/src/features/identity/screens/VerifyEmailScreen.tsx`, `apps/mobile/src/features/identity/screens/ResetPasswordScreen.tsx`, `apps/mobile/src/features/identity/identity.test.tsx`.
- **Acceptance:** Same account flows work on installed builds; verified links return to correct environment and SecureStore restores sessions.
- **Verify (N):** Exercise cold/warm deep links, expired link, invalid OTP, app restart and offline login on Android/iOS.

### T025 — Enforce session and active-school guards

- [ ] **Complete T025**
- **Dependencies:** T019,T021. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/middleware/session.ts`, `apps/api/src/middleware/school-context.ts`, `apps/api/src/modules/school-access/policy.ts`, `apps/api/src/modules/identity/routes.ts`, `apps/api/tests/integration/access-guards.test.ts`.
- **Acceptance:** Protected routes deny by default; /me exposes allowed DTOs/memberships; each request checks session, user, school and membership status.
- **Verify (I):** Test forged roles/school header, anonymous requests, suspended school/user and stale sessions with caching disabled.

### T026 — Implement profile and context switching

- [ ] **Complete T026**
- **Dependencies:** T023,T024,T025. **Requirements:** R01,R03. **Estimated scope:** M.
- **Files:** `apps/web/src/features/identity/pages/ProfilePage.tsx`, `apps/web/src/features/identity/components/SchoolSwitcher.tsx`, `apps/mobile/src/features/identity/screens/ProfileScreen.tsx`, `apps/mobile/src/features/identity/components/SchoolSwitcher.tsx`, `packages/api-client/src/context.ts`.
- **Acceptance:** Basic profile edits are allowlisted; switching school/role clears prior context before rendering; role options come from server memberships.
- **Verify (W+N):** Verify multi-school teacher-parent switches, profile mass-assignment denial and no previous-school flash/cache leakage.

**Checkpoint P02.3 — through T026**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T027 — Implement revocation and recovery cleanup

- [ ] **Complete T027**
- **Dependencies:** T025,T026. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/identity/sessions.service.ts`, `apps/api/src/auth/auth.ts`, `apps/web/src/lib/auth-client.ts`, `apps/mobile/src/lib/auth-client.ts`, `apps/api/tests/integration/session-revocation.test.ts`.
- **Acceptance:** Logout and password reset revoke required sessions; clients clear context on 401; disabled membership immediately loses school access.
- **Verify (I+N):** Reset on one device then replay old session on another; verify logout/cold restart and suspended membership denial.

### T028 — Implement admin role controls and MFA

- [ ] **Complete T028**
- **Dependencies:** T025,T027. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/school-access/routes.ts`, `apps/api/src/modules/school-access/service.ts`, `apps/api/src/auth/auth.ts`, `apps/web/src/features/identity/pages/SecurityPage.tsx`, `apps/api/tests/integration/admin-security.test.ts`.
- **Acceptance:** Admin manages permitted roles with recent auth and TOTP; last-active-admin invariant holds under concurrency; MFA recovery is controlled.
- **Verify (I+W):** Test concurrent admin demotions, non-admin escalation, incomplete MFA session, recovery-code reuse and expired recent-auth window.

### T028a — Complete native MFA and security settings

- [ ] **Complete T028a**
- **Dependencies:** T028,T024. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/identity/screens/MfaChallengeScreen.tsx`, `apps/mobile/src/features/identity/screens/SecurityScreen.tsx`, `apps/mobile/src/lib/auth-client.ts`, `apps/mobile/src/features/identity/mfa.test.tsx`, `tests/e2e/mobile/mfa.yaml`.
- **Acceptance:** MFA-enrolled admin can sign in on native, manage supported security settings and recover with a single-use code; challenge session grants no school access.
- **Verify (N):** Test cold start during challenge, wrong TOTP, replayed recovery code, offline challenge and completion on both platforms.

**Checkpoint P02.4 — through T028a**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T028b — Build staff user and invitation administration

- [ ] **Complete T028b**
- **Dependencies:** T021,T028. **Requirements:** R01,R02. **Estimated scope:** M.
- **Files:** `apps/web/src/features/identity/pages/MembersPage.tsx`, `apps/web/src/features/identity/components/InviteForm.tsx`, `apps/web/src/features/identity/components/RoleEditor.tsx`, `apps/web/src/features/identity/members.test.tsx`, `tests/e2e/web/members.spec.ts`.
- **Acceptance:** Admin invites/resends/revokes and activates/suspends permitted school memberships using recent-auth controls; last admin cannot be removed.
- **Verify (W+I):** Invite each role through UI; revoke pending invite, suspend active teacher and verify immediate denial; audit every change.

### T029 — Add CSRF, origin and request throttling

- [ ] **Complete T029**
- **Dependencies:** T025. **Requirements:** R01,R11,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/middleware/csrf.ts`, `apps/api/src/middleware/rate-limit.ts`, `apps/api/src/app.ts`, `apps/api/src/config/env.ts`, `apps/api/tests/integration/http-security.test.ts`.
- **Acceptance:** Every business mutation requires session-bound CSRF token on web/native; /csrf is protected/no-store; foreign/null Origin and forged native bypass are rejected; shared limits cover replicas.
- **Verify (I):** Test missing/null Origin, stolen cookie without CSRF, forged native headers, legitimate Expo token transport, 429, body cap and proxy-IP configuration.

### T030 — Complete the authentication acceptance matrix

- [ ] **Complete T030**
- **Dependencies:** T023,T024,T026,T027,T028,T029,T028a,T028b. **Requirements:** R01,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/auth.spec.ts`, `tests/e2e/mobile/auth.yaml`, `docs/verification/T030.md`, `docs/permissions.md`.
- **Acceptance:** All four roles pass invitation/sign-in/reset/logout; revoked access, cross-school requests and role changes are covered in a repeatable suite.
- **Verify (W+N+I):** Run auth E2E on web/Android/iOS; inspect logs for OTP/token leaks; reconcile remaining Q04/Q05/Q11 before school rollout.

**Checkpoint P02.5 — through T030**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P02 exit gate:** Every role can authenticate; account status and tenant/relationship checks are server enforced. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P03 — School administration and academic records

**Exit outcome:** A school can configure its year and establish correct students, teachers, enrolments and guardian permissions.

### T031 — Create academic setup constraints

- [ ] **Complete T031**
- **Dependencies:** T004,T019,T030. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_academics/migration.sql`, `packages/contracts/src/academics.ts`, `apps/api/tests/integration/academics-schema.test.ts`.
- **Acceptance:** Years, calendar dates, class levels, sections, subjects and assignments use tenant-safe references; only one active year per school.
- **Verify (DB+I):** Test invalid dates, duplicate section/code, cross-school assignment and concurrent active-year creation.

### T032 — Build year and calendar setup slice

- [ ] **Complete T032**
- **Dependencies:** T031. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/calendar.routes.ts`, `apps/api/src/modules/academics/calendar.service.ts`, `apps/web/src/features/academics/pages/CalendarPage.tsx`, `apps/api/tests/integration/calendar.test.ts`, `apps/web/src/features/academics/calendar.test.tsx`.
- **Acceptance:** Admin configures school timezone/year/open days/holidays; archived years cannot be casually edited; invalid intervals show field errors.
- **Verify (I+W):** Create a year and closure day through UI, reload and verify exact school dates; test unauthorized updates.

### T033 — Build class, section and subject setup slice

- [ ] **Complete T033**
- **Dependencies:** T031. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/structure.routes.ts`, `apps/api/src/modules/academics/structure.service.ts`, `apps/web/src/features/academics/pages/StructurePage.tsx`, `apps/api/tests/integration/structure.test.ts`, `apps/web/src/features/academics/structure.test.tsx`.
- **Acceptance:** Admin creates/edits/archives academic structure; referenced entities cannot be destructively removed and duplicate names/codes are explained.
- **Verify (I+W):** Complete setup via browser; try deleting a referenced section and assigning another school's subject.

**Checkpoint P03.1 — through T033**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T034 — Assign teachers and register delegates

- [ ] **Complete T034**
- **Dependencies:** T033,T028. **Requirements:** R02,R04. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/teachers.routes.ts`, `apps/api/src/modules/academics/teachers.service.ts`, `apps/web/src/features/academics/pages/TeacherAssignmentsPage.tsx`, `apps/api/tests/integration/teacher-assignments.test.ts`, `docs/permissions.md`.
- **Acceptance:** Subject teaching and daily-register authority are distinct, dated permissions; revoked assignment no longer authorizes a teacher.
- **Verify (I+W):** Assign subject and homeroom/delegate roles, test start/end boundaries and cross-school teacher IDs.

### T035 — Introduce student and enrolment model

- [ ] **Complete T035**
- **Dependencies:** T031. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_students/migration.sql`, `packages/contracts/src/academics.ts`, `apps/api/tests/integration/enrollment-schema.test.ts`.
- **Acceptance:** Students have school-unique admission number and optional account; enrolments enforce dated, nonoverlapping section membership.
- **Verify (DB+I):** Test same admission number in different schools, duplicate within school, overlapping enrolment and foreign-school section.

### T036 — Build manual student enrolment slice

- [ ] **Complete T036**
- **Dependencies:** T033,T035. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/students.routes.ts`, `apps/api/src/modules/academics/students.service.ts`, `apps/web/src/features/academics/pages/StudentsPage.tsx`, `apps/web/src/features/academics/components/StudentForm.tsx`, `apps/api/tests/integration/students.test.ts`.
- **Acceptance:** Admin creates student plus initial enrolment atomically; roster is paginated/searchable; student account linking is explicit.
- **Verify (I+W):** Create/search/edit a record in browser; verify failed enrolment leaves no partial student and profile exposes minimal fields.

**Checkpoint P03.2 — through T036**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T036a — Bind and revoke pupil login accounts

- [ ] **Complete T036a**
- **Dependencies:** T036,T021,T028b. **Requirements:** R01,R02,R08,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/student-accounts.service.ts`, `apps/api/src/modules/academics/students.routes.ts`, `apps/web/src/features/academics/components/StudentAccountForm.tsx`, `apps/api/tests/integration/student-accounts.test.ts`, `packages/contracts/src/academics.ts`.
- **Acceptance:** Verified invitation binds the intended pupil; admin can associate/reassociate/unlink with unique same-school relation, recent auth and audit; pupil cannot self-select record.
- **Verify (I+W):** Accept student invite and read own record; test duplicate binding, foreign pupil, mismatched email, race and old account access after reassociation.

### T037 — Support transfer and withdrawal history

- [ ] **Complete T037**
- **Dependencies:** T036. **Requirements:** R02,R04,R08. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/enrollment.service.ts`, `apps/api/src/modules/academics/students.routes.ts`, `apps/web/src/features/academics/components/TransferForm.tsx`, `apps/api/tests/integration/transfers.test.ts`, `docs/specs/academics.md`.
- **Acceptance:** Transfer closes/opens intervals atomically; withdrawal removes current access without deleting history; year archive blocks new writes.
- **Verify (I+W):** Test concurrent transfer, same-day boundary and historical roster lookup; document manual next-year promotion workflow.

### T038 — Build guardian verification slice

- [ ] **Complete T038**
- **Dependencies:** T035,T025. **Requirements:** R02,R12. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_guardian_links/migration.sql`, `apps/api/src/modules/academics/guardians.service.ts`, `apps/web/src/features/academics/pages/GuardianLinksPage.tsx`, `apps/api/tests/integration/guardians.test.ts`.
- **Acceptance:** Staff verifies/links/revokes guardians; multiple children/guardians work; unrelated parents and users in another school remain denied.
- **Verify (I+W):** Link parent to two children, revoke one, then attempt direct child ID read; no link is inferred from matching contact data.

**Checkpoint P03.3 — through T038**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T039 — Build parent child selection

- [ ] **Complete T039**
- **Dependencies:** T038,T026. **Requirements:** R02,R03. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/guardians.routes.ts`, `apps/web/src/features/academics/components/ChildSwitcher.tsx`, `apps/mobile/src/features/academics/components/ChildSwitcher.tsx`, `packages/api-client/src/children.ts`, `tests/e2e/web/guardian-access.spec.ts`.
- **Acceptance:** Server returns only active verified children; child context is explicit and caches clear before switching; no-children state guides user to school.
- **Verify (W+N):** Switch children/schools on web/mobile, revoke a link mid-session and verify no stale-child private view remains.

### T040 — Implement validated CSV import preview

- [ ] **Complete T040**
- **Dependencies:** T036,T034. **Requirements:** R02. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/academics/import-preview.service.ts`, `apps/api/src/modules/academics/imports.routes.ts`, `packages/contracts/src/academics.ts`, `apps/web/src/features/academics/pages/ImportPage.tsx`, `apps/api/tests/integration/import-preview.test.ts`.
- **Acceptance:** Bounded CSV preview reports duplicate admission numbers, unknown sections and row errors; no student/enrolment changes before commit.
- **Verify (I+W):** Test malformed encoding/headers, formula-like cells, 1,001-row rejection and mixed valid/invalid rows.

### T041 — Commit imports with replay protection

- [ ] **Complete T041**
- **Dependencies:** T040,T037. **Requirements:** R02,R12. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_imports/migration.sql`, `apps/api/src/modules/academics/import-commit.service.ts`, `apps/web/src/features/academics/pages/ImportPage.tsx`, `apps/api/tests/integration/import-commit.test.ts`.
- **Acceptance:** Preview state is persisted; commit revalidates references and is atomic/idempotent; reconciliation counts and safe error export are shown.
- **Verify (I+W):** Double-submit same batch, change roster between preview/commit and inject transaction failure; reconcile exact student/enrolment counts.

**Checkpoint P03.4 — through T041**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T042 — Verify school onboarding end to end

- [ ] **Complete T042**
- **Dependencies:** T032,T034,T037,T039,T041,T036a. **Requirements:** R02,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/onboarding.spec.ts`, `packages/database/prisma/seed.ts`, `docs/runbooks/onboarding.md`, `docs/verification/T042.md`.
- **Acceptance:** A fresh synthetic school is ready for teaching without manual database edits; permissions work with transfers and multiple guardians.
- **Verify (W+I):** Run first admin→year→classes→teachers→students→guardian links; verify teacher roster and parent child list against expected records.

**Checkpoint P03.5 — through T042**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P03 exit gate:** A school can configure its year and establish correct students, teachers, enrolments and guardian permissions. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P04 — Attendance as the first complete teaching slice

**Exit outcome:** A teacher submits a register, a parent sees it, and historical reports reconcile exactly.

### T043 — Expose school-scoped audit inspection

- [ ] **Complete T043**
- **Dependencies:** T019a. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/school-access/audit.routes.ts`, `apps/api/src/modules/school-access/audit.service.ts`, `apps/web/src/features/identity/pages/AuditPage.tsx`, `apps/api/tests/integration/audit-access.test.ts`.
- **Acceptance:** Admin can inspect paginated permitted audit summaries by action/date; log is append-only and no credentials or unnecessary pupil content appear.
- **Verify (I+W):** Run after T043 creates storage: test school isolation, non-admin denial, bounded filters and no audit edit/delete endpoint.

### T044 — Implement daily register persistence and API

- [ ] **Complete T044**
- **Dependencies:** T019a,T034,T037. **Requirements:** R04. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_attendance/migration.sql`, `packages/contracts/src/attendance.ts`, `apps/api/src/modules/attendance/service.ts`, `apps/api/tests/integration/attendance.test.ts`.
- **Acceptance:** Tenant-safe snapshot register supports unmarked draft, explicit statuses and complete submission with version check; only authorized markers/date range allowed.
- **Verify (DB+I):** Test holiday/future date, stale version, wrong section, missing entries and simultaneous create/submit; verify atomic audit/outbox/result.

### T045 — Build web register marking

- [ ] **Complete T045**
- **Dependencies:** T044. **Requirements:** R04. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/attendance/routes.ts`, `apps/web/src/features/attendance/pages/RegisterPage.tsx`, `apps/web/src/features/attendance/components/RegisterRow.tsx`, `apps/web/src/features/attendance/register.test.tsx`, `apps/web/src/lib/query-keys.ts`.
- **Acceptance:** Teacher selects date/section, marks rows/all-present explicitly, saves draft and submits; unsaved/conflict/failed-save states are visible.
- **Verify (W):** Keyboard-mark a 40-student register, reload draft, submit, retry a timed-out response and open conflicting second browser session.

**Checkpoint P04.1 — through T045**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T046 — Build native register marking

- [ ] **Complete T046**
- **Dependencies:** T044,T045. **Requirements:** R04,R11. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/attendance/screens/RegisterScreen.tsx`, `apps/mobile/src/features/attendance/components/RegisterRow.tsx`, `apps/mobile/src/features/attendance/hooks/use-register.ts`, `apps/mobile/src/features/attendance/register.test.tsx`, `tests/e2e/mobile/attendance.yaml`.
- **Acceptance:** Teacher marks/submits assigned section with touch-friendly rows, visible counts and no false success when disconnected.
- **Verify (N):** Complete register on Android/iOS with keyboard open, slow network, app backgrounding and duplicate tap; measure task duration.

### T047 — Expose scoped attendance history

- [ ] **Complete T047**
- **Dependencies:** T044,T039. **Requirements:** R05. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/attendance/history.service.ts`, `apps/api/src/modules/attendance/routes.ts`, `apps/web/src/features/attendance/pages/HistoryPage.tsx`, `apps/mobile/src/features/attendance/screens/HistoryScreen.tsx`, `apps/api/tests/integration/attendance-history.test.ts`.
- **Acceptance:** Calendar distinguishes unmarked/absent/excused; self/linked-child/teacher views are scoped and refresh within agreed foreground interval.
- **Verify (I+W+N):** Test transfer month, revoked guardian, unrelated student ID, month/year boundary and a teacher save appearing in parent view.

### T048 — Implement monthly aggregates and CSV reports

- [ ] **Complete T048**
- **Dependencies:** T047. **Requirements:** R05. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/attendance/reports.service.ts`, `apps/api/src/modules/attendance/report-policy.ts`, `packages/contracts/src/attendance.ts`, `apps/api/tests/integration/attendance-reports.test.ts`, `apps/api/src/modules/attendance/reports.test.ts`.
- **Acceptance:** Report totals follow school-day/enrolment rules and show marking coverage; zero denominator is unavailable; CSV output is safely escaped.
- **Verify (U+I):** Compare known fixture totals by hand, including late/excused/missing registers; test tenant scope and formula-injection prefixes.

**Checkpoint P04.2 — through T048**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T049 — Build private PDF/CSV report requests

- [ ] **Complete T049**
- **Dependencies:** T048,T019a. **Requirements:** R05. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/attendance/report-routes.ts`, `apps/api/src/jobs/export-report.ts`, `apps/web/src/features/attendance/pages/ReportsPage.tsx`, `apps/api/tests/integration/report-jobs.test.ts`, `docs/specs/attendance.md`.
- **Acceptance:** Authorized report request records a bounded export job and status; repeat intent creates one report; generated files remain private.
- **Verify (I+W):** Run export handler directly in integration tests until worker T058 connects it; inspect PDF pagination/totals and reauthorize download when T061 lands.

### T050 — Support audited register and roster corrections

- [ ] **Complete T050**
- **Dependencies:** T044,T037. **Requirements:** R04,R05. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/attendance/corrections.service.ts`, `apps/api/src/modules/attendance/routes.ts`, `apps/web/src/features/attendance/components/CorrectionDialog.tsx`, `apps/mobile/src/features/attendance/components/CorrectionDialog.tsx`, `apps/api/tests/integration/attendance-corrections.test.ts`.
- **Acceptance:** Today's teacher correction requires reason; older/submitted roster changes require admin audit; draft roster reconciliation increments version and preserves history.
- **Verify (I+W+N):** Test backdated enrolment, transfer/withdrawal after draft and submitted register, concurrent correction and report recalculation without lost history.

### T051 — Verify attendance acceptance and query budgets

- [ ] **Complete T051**
- **Dependencies:** T046,T047,T048,T049,T050,T043. **Requirements:** R03–R05,R11,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/attendance.spec.ts`, `tests/load/attendance-burst.js`, `docs/verification/T051.md`, `docs/user-guides/teacher.md`, `docs/user-guides/parent.md`.
- **Acceptance:** Full teacher→student/parent path passes; queries are bounded on annual data and report numbers agree with source registers.
- **Verify (W+N+I):** Run role E2E and initial 30-register burst baseline; review metrics, denied accesses, version conflicts and measured marking time.

**Checkpoint P04.3 — through T051**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P04 exit gate:** A teacher submits a register, a parent sees it, and historical reports reconcile exactly. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P05 — Timetable management and views

**Exit outcome:** School publishes an effective weekly timetable with no teacher/section/room double bookings.

### T052 — Define timetable versions and conflict rules

- [ ] **Complete T052**
- **Dependencies:** T031,T034,T019a. **Requirements:** R06. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_timetable/migration.sql`, `packages/contracts/src/timetable.ts`, `apps/api/src/modules/timetable/conflicts.ts`, `apps/api/src/modules/timetable/conflicts.test.ts`.
- **Acceptance:** Draft/published versions have effective intervals; time ranges validate teacher, section and optional room overlap.
- **Verify (U+DB):** Unit-test adjacent/nonadjacent overlap, invalid durations, cross-date versions, holiday semantics and teacher assignment boundaries.

### T053 — Implement atomic timetable publication

- [ ] **Complete T053**
- **Dependencies:** T052. **Requirements:** R06. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/timetable/routes.ts`, `apps/api/src/modules/timetable/service.ts`, `apps/api/src/modules/timetable/policy.ts`, `apps/api/tests/integration/timetable.test.ts`, `docs/specs/timetable.md`.
- **Acceptance:** Admin edits draft and publishes atomically under school/year lock; conflicts include actionable details; historic version remains queryable.
- **Verify (I):** Publish conflicting drafts concurrently; test stale version, foreign-school IDs and historical effective-date lookup.

### T054 — Build timetable editor and preview

- [ ] **Complete T054**
- **Dependencies:** T053. **Requirements:** R06. **Estimated scope:** M.
- **Files:** `apps/web/src/features/timetable/pages/TimetableEditorPage.tsx`, `apps/web/src/features/timetable/components/PeriodForm.tsx`, `apps/web/src/features/timetable/components/WeeklyGrid.tsx`, `apps/web/src/features/timetable/editor.test.tsx`.
- **Acceptance:** Admin creates periods/breaks and previews before publishing; keyboard editing is available without drag-and-drop.
- **Verify (W):** Create a full week, trigger teacher/room conflict and correct it; verify reload and published result.

**Checkpoint P05.1 — through T054**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T055 — Build daily and weekly timetable views

- [ ] **Complete T055**
- **Dependencies:** T053,T039. **Requirements:** R06. **Estimated scope:** M.
- **Files:** `apps/web/src/features/timetable/pages/TimetablePage.tsx`, `apps/mobile/src/features/timetable/screens/TimetableScreen.tsx`, `packages/api-client/src/timetable.ts`, `apps/api/tests/integration/timetable-visibility.test.ts`.
- **Acceptance:** Teacher/student/parent receive scoped schedules; colours include subject labels and free/closed days are explicit.
- **Verify (W+N+I):** Compare both clients with published fixtures across roles, school/child switching and withdrawn enrolment.

### T056 — Implement current-period timing

- [ ] **Complete T056**
- **Dependencies:** T055. **Requirements:** R06. **Estimated scope:** M.
- **Files:** `packages/contracts/src/school-time.ts`, `packages/contracts/src/school-time.test.ts`, `apps/web/src/features/timetable/hooks/use-current-period.ts`, `apps/mobile/src/features/timetable/hooks/use-current-period.ts`, `apps/api/src/modules/timetable/service.ts`.
- **Acceptance:** Current class/countdown uses school zone and server offset, pauses correctly on background and respects effective date/closure.
- **Verify (U+W+N):** Test DST gap/fold, midnight, device clock skew, break boundary and foreground resume using a fixed clock.

### T057 — Verify timetable lifecycle

- [ ] **Complete T057**
- **Dependencies:** T054,T055,T056. **Requirements:** R06,R11,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/timetable.spec.ts`, `tests/e2e/mobile/timetable.yaml`, `docs/user-guides/admin.md`, `docs/verification/T057.md`.
- **Acceptance:** Draft→publish→future replacement→historical lookup works with permission and conflict enforcement.
- **Verify (W+N):** Run browser/native flows, inspect grid on narrow screens and test stale client after new version publication.

**Checkpoint P05.2 — through T057**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P05 exit gate:** School publishes an effective weekly timetable with no teacher/section/room double bookings. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P06 — Secure attachments, notices and durable inbox

**Exit outcome:** Scanned immutable files can be shared with precisely defined audiences; notification records survive delivery failures.

### T058 — Implement the durable worker and fenced leases

- [ ] **Complete T058**
- **Dependencies:** T019a,T049. **Requirements:** R05,R09,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/platform/jobs.ts`, `apps/api/src/worker.ts`, `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_jobs/migration.sql`, `apps/api/tests/integration/jobs.test.ts`.
- **Acceptance:** Bounded claims, expiring leases/generation fencing, retries/backoff and terminal state work; export handler is registered.
- **Verify (I):** Kill worker after claim, reclaim job, resume stale worker and reject its state writes; test duplicate event, shutdown and retry exhaustion.

### T059 — Create upload intent and completion API

- [ ] **Complete T059**
- **Dependencies:** T058,T025. **Requirements:** R07,R08,R12. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_files/migration.sql`, `packages/contracts/src/files.ts`, `apps/api/src/modules/files/routes.ts`, `apps/api/tests/integration/file-intents.test.ts`.
- **Acceptance:** Authorized intent has random private key, type/size/quota limits and expiry; completion binds actual object version/checksum and queues scan.
- **Verify (I):** Test oversize, guessed key, other-school owner, repeated completion, invalid metadata and still-valid upload URL overwrites.

### T060 — Scan and promote immutable file versions

- [ ] **Complete T060**
- **Dependencies:** T059. **Requirements:** R07,R08,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/jobs/scan-file.ts`, `apps/api/src/modules/files/storage.ts`, `apps/api/src/modules/files/service.ts`, `apps/api/tests/integration/file-scanning.test.ts`, `docs/decisions/0005-files.md`.
- **Acceptance:** Scan/promote the identical immutable version; magic type and malware result gate READY; scanner outage fails closed and image metadata is stripped.
- **Verify (I):** Replace quarantine object during scanning and verify replacement is never promoted; test stale lease, bad signature, scan timeout and idempotent promotion.

**Checkpoint P06.1 — through T060**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T061 — Authorize downloads and safe cleanup

- [ ] **Complete T061**
- **Dependencies:** T060,T049. **Requirements:** R05,R07,R08,R12. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/files/policy.ts`, `apps/api/src/modules/files/routes.ts`, `apps/api/src/jobs/cleanup.ts`, `apps/api/tests/integration/file-access.test.ts`, `apps/api/src/jobs/export-report.ts`.
- **Acceptance:** Download rechecks owning-resource policy and emits ≤60s URL; cleanup removes only expired unreferenced versions; report outputs use same private file model.
- **Verify (I):** Try revoked guardian, guessed file ID, archived entity, expired URL and cleanup racing a submission link; complete async report download end to end.

### T062 — Build web upload and attachment states

- [ ] **Complete T062**
- **Dependencies:** T059,T060,T061. **Requirements:** R07,R08,R11. **Estimated scope:** M.
- **Files:** `apps/web/src/features/files/components/UploadField.tsx`, `apps/web/src/features/files/components/AttachmentList.tsx`, `apps/web/src/features/files/hooks/use-upload.ts`, `apps/web/src/features/files/upload.test.tsx`, `packages/api-client/src/files.ts`.
- **Acceptance:** Upload shows progress/cancel/retry/scanning/rejected/ready; only READY files can attach; errors preserve draft text.
- **Verify (W):** Test network interruption, cancellation, malicious filename, oversized file and signed URL expiry without exposing object URLs in logs.

### T063 — Build native photo/document upload

- [ ] **Complete T063**
- **Dependencies:** T059,T060,T061. **Requirements:** R07,R08,R11. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/files/components/UploadField.tsx`, `apps/mobile/src/features/files/hooks/use-upload.ts`, `apps/mobile/src/features/files/image-normalization.ts`, `apps/mobile/src/features/files/upload.test.tsx`, `tests/e2e/mobile/files.yaml`.
- **Acceptance:** Camera/document picker works with consent, size checks and HEIC conversion/rejection; upload resumes through an explicit retry without duplicate attachment.
- **Verify (N):** Test real iPhone/Android camera, denied permission, PDF picker, background interruption, scan rejection and large image handling.

**Checkpoint P06.2 — through T063**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T064 — Define notice lifecycle and audience predicate

- [ ] **Complete T064**
- **Dependencies:** T031,T038,T019a. **Requirements:** R07,R09. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_notices/migration.sql`, `packages/contracts/src/notices.ts`, `apps/api/src/modules/notices/policy.ts`, `apps/api/src/modules/notices/policy.test.ts`.
- **Acceptance:** Audience groups use OR between groups and AND for role+section within each group; parent section match uses verified active child links; teacher scope limits creation.
- **Verify (U+DB):** Test Parents+Section A, multi-role user, no section selector, revoked guardian, transfer and archived/expired notice.

### T065 — Build notice composition and publication

- [ ] **Complete T065**
- **Dependencies:** T064,T062,T058. **Requirements:** R07. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/notices/routes.ts`, `apps/api/src/modules/notices/service.ts`, `apps/web/src/features/notices/pages/NoticeComposerPage.tsx`, `apps/mobile/src/features/notices/screens/NoticeComposerScreen.tsx`, `apps/api/tests/integration/notices.test.ts`.
- **Acceptance:** Draft/preview/publish/archive follows policy; READY attachments link transactionally; one publication event and audience count match the exact read predicate.
- **Verify (I+W+N):** Publish urgent/general/event examples on both clients; test duplicate publish, unauthorized audience widening and unscanned attachment.

### T066 — Build scoped notice feeds and detail

- [ ] **Complete T066**
- **Dependencies:** T065,T039. **Requirements:** R07. **Estimated scope:** M.
- **Files:** `apps/web/src/features/notices/pages/NoticesPage.tsx`, `apps/web/src/features/notices/pages/NoticeDetailPage.tsx`, `apps/mobile/src/features/notices/screens/NoticesScreen.tsx`, `apps/mobile/src/features/notices/screens/NoticeDetailScreen.tsx`, `apps/api/tests/integration/notice-visibility.test.ts`.
- **Acceptance:** Role/section feeds paginate and filter categories; detail/download consistently recheck access; expired/archived items disappear.
- **Verify (W+N+I):** Test same notice as intended parent/unrelated parent/teacher, publication edit and withdrawn-child links; verify long text and empty feeds.

**Checkpoint P06.3 — through T066**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T067 — Create inbox fanout and delivery records

- [ ] **Complete T067**
- **Dependencies:** T058,T065. **Requirements:** R09. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_notifications/migration.sql`, `apps/api/src/modules/notifications/service.ts`, `apps/api/src/jobs/deliver-notification.ts`, `apps/api/tests/integration/notification-fanout.test.ts`.
- **Acceptance:** Outbox produces unique event/recipient inbox records with bounded batches; current audience is rechecked and delivery failure cannot roll back publication.
- **Verify (I):** Replay/crash fanout mid-batch; assert one inbox per recipient, no restricted body persistence and correct revoked-recipient handling.

### T068 — Build inbox and read/unread states

- [ ] **Complete T068**
- **Dependencies:** T067. **Requirements:** R09. **Estimated scope:** M.
- **Files:** `packages/contracts/src/notifications.ts`, `apps/api/src/modules/notifications/routes.ts`, `apps/web/src/features/notifications/pages/InboxPage.tsx`, `apps/mobile/src/features/notifications/screens/InboxScreen.tsx`, `apps/api/tests/integration/inbox.test.ts`.
- **Acceptance:** Own inbox paginates, read state is idempotent and unread count is correct; unavailable entity link does not reveal hidden content.
- **Verify (I+W+N):** Test another user's notification ID, concurrent mark-read, expired resource, no push permission and school filtering.

### T069 — Verify notice and attachment acceptance

- [ ] **Complete T069**
- **Dependencies:** T063,T066,T068. **Requirements:** R05,R07,R09,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/notices.spec.ts`, `tests/e2e/mobile/notices.yaml`, `docs/verification/T069.md`, `docs/user-guides/admin.md`, `docs/runbooks/jobs.md`.
- **Acceptance:** Author→scan→publish→recipient inbox→authorized download works; failed scanner/provider jobs are diagnosable and safely replayable.
- **Verify (W+N+I):** Run both clients, test scanner outage/recovery and operator retry; inspect PDF report output from T049 after worker/storage integration.

**Checkpoint P06.4 — through T069**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P06 exit gate:** Scanned immutable files can be shared with precisely defined audiences; notification records survive delivery failures. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P07 — Homework, submissions and released grading

**Exit outcome:** Teacher publishes, student submits, teacher grades and verified parent sees only released results.

### T070 — Create assignment and recipient model

- [ ] **Complete T070**
- **Dependencies:** T004,T034,T035,T019a. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_assignments/migration.sql`, `packages/contracts/src/assignments.ts`, `apps/api/src/modules/assignments/policy.ts`, `apps/api/tests/integration/assignment-schema.test.ts`.
- **Acceptance:** Assignment lifecycle and recipient snapshots use teacher/subject/section ownership; dueAt and positive decimal totalMarks validate.
- **Verify (DB+I):** Test cross-school relationships, assignment access outside roster and deadline normalization across timezone boundaries.

### T071 — Implement assignment publication and roster changes

- [ ] **Complete T071**
- **Dependencies:** T070,T061,T067. **Requirements:** R08,R09. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/assignments/routes.ts`, `apps/api/src/modules/assignments/service.ts`, `apps/api/src/modules/assignments/recipients.service.ts`, `apps/api/tests/integration/assignments.test.ts`, `docs/specs/assignments.md`.
- **Acceptance:** Publish snapshots recipients and links READY files with one event; explicit add-recipient supports later enrolments; closed/archive transitions validate.
- **Verify (I):** Test new/withdrawn pupil, concurrent publish, due-date edit and marks edit after submissions; deduplicated notifications use current access.

### T072 — Build web assignment teaching and reading views

- [ ] **Complete T072**
- **Dependencies:** T071. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/web/src/features/assignments/pages/AssignmentListPage.tsx`, `apps/web/src/features/assignments/pages/AssignmentComposerPage.tsx`, `apps/web/src/features/assignments/pages/AssignmentDetailPage.tsx`, `apps/web/src/features/assignments/assignments.test.tsx`.
- **Acceptance:** Teacher composes/previews/publishes/closes; student/parent see permitted details and Pending/Submitted/Graded labels.
- **Verify (W):** Run role-specific views with empty list, expired session, late deadline, attachments and form errors.

**Checkpoint P07.1 — through T072**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T073 — Build native assignment teaching and reading views

- [ ] **Complete T073**
- **Dependencies:** T071,T063. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/assignments/screens/AssignmentListScreen.tsx`, `apps/mobile/src/features/assignments/screens/AssignmentComposerScreen.tsx`, `apps/mobile/src/features/assignments/screens/AssignmentDetailScreen.tsx`, `apps/mobile/src/features/assignments/assignments.test.tsx`.
- **Acceptance:** Teacher and learners complete native assignment navigation/composition; deadline and child/section context are always visible.
- **Verify (N):** Publish/view on Android/iOS with long text, keyboard, denied upload permission and screen reader.

### T074 — Implement versioned student submissions

- [ ] **Complete T074**
- **Dependencies:** T071. **Requirements:** R08,R11. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_submissions/migration.sql`, `apps/api/src/modules/assignments/submissions.service.ts`, `apps/api/src/modules/assignments/submissions.routes.ts`, `apps/api/tests/integration/submissions.test.ts`.
- **Acceptance:** Own recipient can create/replace attempt before grade/closure; lateAtSubmission uses server clock; READY attachments and stable keys prevent duplicate attempts.
- **Verify (DB+I):** Race resubmissions, retry after lost response, submit at deadline boundary and reject parent/other-pupil/unscanned-file attempts.

### T075 — Build web submission workflow

- [ ] **Complete T075**
- **Dependencies:** T074,T072,T062. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/web/src/features/assignments/components/SubmissionForm.tsx`, `apps/web/src/features/assignments/components/SubmissionHistory.tsx`, `apps/web/src/features/assignments/hooks/use-submission.ts`, `apps/web/src/features/assignments/submission.test.tsx`.
- **Acceptance:** Student reviews selected files/text, submits and sees server receipt/late state; replacement preserves prior attempt history.
- **Verify (W):** Test double-click, slow upload, scan pending, interrupted request and server-rejected replacement without losing draft text.

**Checkpoint P07.2 — through T075**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T076 — Build native submission workflow

- [ ] **Complete T076**
- **Dependencies:** T074,T073,T063. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/assignments/components/SubmissionForm.tsx`, `apps/mobile/src/features/assignments/components/SubmissionHistory.tsx`, `apps/mobile/src/features/assignments/hooks/use-submission.ts`, `apps/mobile/src/features/assignments/submission.test.tsx`.
- **Acceptance:** Student submits camera/PDF work and receives durable confirmation; failed/unknown result reconciles before retry.
- **Verify (N):** Real-device photo submission, background app during upload, retry same intent and confirm exactly one submitted attempt.

### T077 — Implement grade draft, release and reopening

- [ ] **Complete T077**
- **Dependencies:** T074,T067. **Requirements:** R08,R09. **Estimated scope:** M.
- **Files:** `packages/database/prisma/schema.prisma`, `packages/database/prisma/migrations/<timestamp>_grades/migration.sql`, `apps/api/src/modules/assignments/grading.service.ts`, `apps/api/src/modules/assignments/grading.routes.ts`, `apps/api/tests/integration/grading.test.ts`.
- **Acceptance:** Draft grade references attempt; authorized release validates marks and emits one event; correction/reopening preserve assessed history.
- **Verify (DB+I):** Test negative/over-total marks, draft leakage, concurrent student replacement/grade, wrong teacher, release replay and corrected grade revision.

### T078 — Build web marking and release

- [ ] **Complete T078**
- **Dependencies:** T077,T072. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/web/src/features/assignments/pages/MarkingPage.tsx`, `apps/web/src/features/assignments/components/GradeForm.tsx`, `apps/web/src/features/assignments/components/SubmissionPreview.tsx`, `apps/web/src/features/assignments/grading.test.tsx`.
- **Acceptance:** Teacher sees submitted/pending list, examines authorized files, saves draft and explicitly releases/corrects/reopens with clear attempt identity.
- **Verify (W):** Grade a class fixture; test validation, stale attempt/version, keyboard navigation and unsaved feedback warning.

**Checkpoint P07.3 — through T078**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T079 — Build native marking and release

- [ ] **Complete T079**
- **Dependencies:** T077,T073. **Requirements:** R08. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/assignments/screens/MarkingScreen.tsx`, `apps/mobile/src/features/assignments/components/GradeForm.tsx`, `apps/mobile/src/features/assignments/components/SubmissionPreview.tsx`, `apps/mobile/src/features/assignments/grading.test.tsx`.
- **Acceptance:** Teacher completes principal marking/release/reopen workflow on mobile without desktop-only controls.
- **Verify (N):** Open student attachment, draft/release grade, handle conflict and re-open attempt on Android/iOS with large text.

### T080 — Expose released results to learners and guardians

- [ ] **Complete T080**
- **Dependencies:** T077,T039,T075,T076. **Requirements:** R08,R09. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/assignments/results.service.ts`, `apps/web/src/features/assignments/components/ReleasedGrade.tsx`, `apps/mobile/src/features/assignments/components/ReleasedGrade.tsx`, `apps/api/tests/integration/grade-visibility.test.ts`, `docs/user-guides/student.md`.
- **Acceptance:** Student/linked parent sees only released grade/feedback for assessed attempt; revoked links and archived access follow policy.
- **Verify (I+W+N):** Read before/after release as all roles; test altered student ID, corrected grade and no push permission.

### T081 — Verify homework acceptance journey

- [ ] **Complete T081**
- **Dependencies:** T078,T079,T080. **Requirements:** R08,R09,R11,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/homework.spec.ts`, `tests/e2e/mobile/homework.yaml`, `docs/verification/T081.md`, `docs/user-guides/teacher.md`, `docs/user-guides/parent.md`.
- **Acceptance:** Publish→scan→submit→grade draft→release→parent view passes on both platforms with full audit/event trace.
- **Verify (W+N+I):** Run happy and late/resubmit/reopen paths; verify no draft grade leakage and correct notifications after repeated release requests.

**Checkpoint P07.4 — through T081**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P07 exit gate:** Teacher publishes, student submits, teacher grades and verified parent sees only released results. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P08 — Native push, offline reading and complete dashboards

**Exit outcome:** Communication reaches registered devices; offline reads stay bounded; all role dashboards reflect real scoped data.

### T082 — Register native devices and preferences

- [ ] **Complete T082**
- **Dependencies:** T068,T081,T027. **Requirements:** R09. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/notifications/devices.service.ts`, `apps/api/src/modules/notifications/routes.ts`, `apps/mobile/src/lib/notifications.ts`, `apps/mobile/src/features/notifications/screens/PreferencesScreen.tsx`, `apps/api/tests/integration/devices.test.ts`.
- **Acceptance:** Register installation/token/environment against authenticated user; rotate/reassign atomically and revoke on logout; permission denial is supported.
- **Verify (I+N):** Test two users on one device, token replacement, staging/production separation, logout without network and no-permission inbox access.

### T083 — Send push and process provider receipts

- [ ] **Complete T083**
- **Dependencies:** T082,T058. **Requirements:** R09. **Estimated scope:** M.
- **Files:** `apps/api/src/modules/notifications/push-provider.ts`, `apps/api/src/jobs/deliver-notification.ts`, `apps/api/src/jobs/check-push-receipts.ts`, `apps/api/tests/integration/push-delivery.test.ts`, `docs/runbooks/jobs.md`.
- **Acceptance:** Generic push payloads have receipt tracking, transient retries and invalid-token removal; dedup keys outlive all retry/replay paths.
- **Verify (I+N):** Simulate provider accepted-but-timeout, invalid token, rate limit and receipt failure; observe actual push on physical Android/iPhone.

### T084 — Handle secure notification deep links

- [ ] **Complete T084**
- **Dependencies:** T083,T066,T080. **Requirements:** R09,R12. **Estimated scope:** M.
- **Files:** `apps/mobile/src/lib/deep-links.ts`, `apps/mobile/src/app/_layout.tsx`, `apps/web/src/app/router.tsx`, `tests/e2e/mobile/deep-links.yaml`, `tests/e2e/web/deep-links.spec.ts`.
- **Acceptance:** Cold/warm notification opens the intended school/resource only after authorization; expired session resumes safely after login.
- **Verify (W+N):** Test logged-out, wrong school, withdrawn pupil, revoked guardian, archived entity and forged external redirect destination.

**Checkpoint P08.1 — through T084**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T085 — Implement versioned offline cache storage

- [ ] **Complete T085**
- **Dependencies:** T055,T066,T026. **Requirements:** R10. **Estimated scope:** M.
- **Files:** `apps/mobile/src/storage/database.ts`, `apps/mobile/src/storage/migrations.ts`, `apps/mobile/src/storage/cache-policy.ts`, `apps/mobile/src/lib/offline-cache.ts`, `apps/mobile/src/storage/cache-policy.test.ts`.
- **Acceptance:** SQLite cache partitions by identity/school/child, stores only allowlisted fields and enforces TTL/schema version; opt-in/shared-device choice persists safely.
- **Verify (U+N):** Test 24h expiry, schema migration, corrupt cache recovery, deleted resource and account key collisions without private detail persistence.

### T086 — Connect offline timetable and notices

- [ ] **Complete T086**
- **Dependencies:** T085,T056. **Requirements:** R06,R07,R10. **Estimated scope:** M.
- **Files:** `apps/mobile/src/features/timetable/screens/TimetableScreen.tsx`, `apps/mobile/src/features/notices/screens/NoticesScreen.tsx`, `apps/mobile/src/features/notices/screens/NoticeDetailScreen.tsx`, `apps/mobile/src/features/offline/OfflineBanner.tsx`, `tests/e2e/mobile/offline.yaml`.
- **Acceptance:** Fetched schedule/notice text opens offline with last-sync/expiry labels; uncached items explain limits and files do not silently persist.
- **Verify (N):** Fetch online, disable network, restart app, view cached data, pass TTL and reconnect after notice edit/archive.

### T087 — Enforce local data purge and session boundaries

- [ ] **Complete T087**
- **Dependencies:** T085,T027,T039. **Requirements:** R10,R12. **Estimated scope:** M.
- **Files:** `apps/mobile/src/lib/auth-client.ts`, `apps/mobile/src/lib/offline-cache.ts`, `apps/web/src/lib/query-keys.ts`, `packages/api-client/src/context.ts`, `apps/mobile/src/features/offline/isolation.test.ts`.
- **Acceptance:** Logout/account/school/child changes purge protected caches before rendering; reconnect denial removes restricted data; device dispatch rechecks session status.
- **Verify (U+W+N):** Switch users offline/online and background/restart after logout; revoke link remotely then reconnect; verify no old child content or stale push binding.

**Checkpoint P08.2 — through T087**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T088 — Implement role dashboard queries

- [ ] **Complete T088**
- **Dependencies:** T047,T055,T066,T080. **Requirements:** R03. **Estimated scope:** M.
- **Files:** `packages/contracts/src/dashboards.ts`, `apps/api/src/modules/dashboards/routes.ts`, `apps/api/src/modules/dashboards/service.ts`, `apps/api/src/modules/dashboards/policy.ts`, `apps/api/tests/integration/dashboards.test.ts`.
- **Acceptance:** Admin counts/coverage, teacher classes/tasks, learner homework/timetable and parent child summaries use scoped bounded aggregates; no placeholder fees.
- **Verify (I):** Compare fixture aggregates with source records, zero-data school and school timezone midnight; test wrong role/child and query count budget.

### T089 — Build role dashboard clients

- [ ] **Complete T089**
- **Dependencies:** T088,T087. **Requirements:** R03. **Estimated scope:** M.
- **Files:** `apps/web/src/features/dashboards/pages/DashboardPage.tsx`, `apps/web/src/features/dashboards/dashboard.test.tsx`, `apps/mobile/src/features/dashboards/screens/DashboardScreen.tsx`, `apps/mobile/src/features/dashboards/dashboard.test.tsx`, `docs/design/screen-inventory.md`.
- **Acceptance:** All four roles see meaningful actionable summaries on web/native; multi-role and no-child states are clear, mobile admin links identify web workflows.
- **Verify (W+N):** Verify all role/child combinations, loading/empty/error states, long names, small screens and no stale cached summaries.

### T090 — Exercise slow-network and recovery behaviour

- [ ] **Complete T090**
- **Dependencies:** T084,T086,T087,T089. **Requirements:** R09–R11. **Estimated scope:** M.
- **Files:** `tests/e2e/web/network-recovery.spec.ts`, `tests/e2e/mobile/network-recovery.yaml`, `packages/api-client/src/client.test.ts`, `docs/verification/T090.md`, `docs/decisions/0006-offline.md`.
- **Acceptance:** Timeout/unknown mutation results reconcile without duplicate writes; stale offline state is labeled and online-only saves are honest.
- **Verify (W+N+I):** Throttle/disconnect during register, publish, submit and grade release; restore connection and count database effects/events; record accepted offline revocation limit.

**Checkpoint P08.3 — through T090**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P08 exit gate:** Communication reaches registered devices; offline reads stay bounded; all role dashboards reflect real scoped data. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P09 — Security, accessibility, performance and recovery hardening

**Exit outcome:** The integrated candidate meets safety, usability, load and restore gates with evidence.

### T091 — Run complete relationship authorization matrix

- [ ] **Complete T091**
- **Dependencies:** T090. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `apps/api/tests/integration/authorization-matrix.test.ts`, `docs/permissions.md`, `docs/verification/T091.md`.
- **Acceptance:** Every resource family covers two schools, all roles, revoked links and unaffiliated IDs, including downloads/exports/search/counts/jobs.
- **Verify (I):** Run real-auth/database denial matrix; fix any missing policy and add regression before proceeding.

### T092 — Harden web/session/upload boundaries

- [ ] **Complete T092**
- **Dependencies:** T091. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `apps/api/tests/integration/security-boundaries.test.ts`, `infra/reverse-proxy.conf`, `docs/verification/T092.md`, `docs/privacy/data-inventory.md`.
- **Acceptance:** CSRF/origin, XSS, CSP/CORS, rate limits, signed-file overwrite and sensitive-log checks pass; no reachable unmitigated high/critical dependency issue.
- **Verify (I+W):** Use browser/network and API adversarial checks, lockfile audit and secret scan; document concrete remediation/accepted residual risks.

### T093 — Audit web accessibility and responsive layout

- [ ] **Complete T093**
- **Dependencies:** T089. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/web/accessibility.spec.ts`, `docs/verification/T093.md`, `docs/design/screen-inventory.md`.
- **Acceptance:** Core journeys pass automated serious/critical accessibility checks plus keyboard/screen-reader/zoom review at supported widths.
- **Verify (W):** Run Playwright/axe, tab all forms/dialogs/tables, test 200% zoom and NVDA; record and fix findings with screenshots.

**Checkpoint P09.1 — through T093**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T094 — Audit native accessibility and device support

- [ ] **Complete T094**
- **Dependencies:** T089,T084,T086. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `tests/e2e/mobile/accessibility.yaml`, `docs/verification/T094.md`, `docs/dependencies.md`.
- **Acceptance:** Core native paths work with VoiceOver/TalkBack/dynamic type, camera denial, supported minimum devices and realistic memory limits.
- **Verify (N):** Test physical Android/iPhone plus supported low-end emulator profile; inspect startup/crash/memory behaviour and document unsupported OS explicitly.

### T095 — Measure and tune representative load

- [ ] **Complete T095**
- **Dependencies:** T091,T088,T018a. **Requirements:** R11,R12. **Estimated scope:** M.
- **Files:** `tests/load/pilot.js`, `tests/load/attendance-burst.js`, `docs/verification/T095.md`, `packages/database/prisma/migrations/<timestamp>_measured_indexes/migration.sql`.
- **Acceptance:** 100-session/30-write baseline meets proposed latency targets; 250-session ramp locates saturation without integrity failures.
- **Verify (LOAD):** Record fixture size, network/hardware, p50/p95/p99/error/DB pool metrics and query plans; keep only measured justified optimizations.

### T096 — Complete operational telemetry and alerts

- [ ] **Complete T096**
- **Dependencies:** T083,T095,T016. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `apps/api/src/platform/telemetry.ts`, `apps/api/src/platform/logger.ts`, `docs/runbooks/incidents.md`, `docs/runbooks/jobs.md`, `docs/verification/T096.md`.
- **Acceptance:** Endpoint/dependency latency, queue age, scans and crashes are visible with bounded labels; each actionable alert has owner/runbook.
- **Verify (OPS):** Inject DB/provider/scanner failure, trace request→job, locate root cause from telemetry and test alert routing; inspect redaction.

**Checkpoint P09.2 — through T096**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T097 — Implement privacy requests and retention jobs

- [ ] **Complete T097**
- **Dependencies:** T061,T091,T006. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `scripts/privacy-request.ts`, `apps/api/src/jobs/cleanup.ts`, `apps/api/tests/integration/privacy-lifecycle.test.ts`, `docs/privacy/retention.md`, `docs/runbooks/privacy-requests.md`.
- **Acceptance:** Verified export/correction/deletion covers all stores with legal hold and deletion ledger; TTL jobs do not erase retained educational evidence.
- **Verify (I+OPS):** Run synthetic pupil export/deletion, inspect files/auth/device/cache records, verify retention overrides and backup deletion-replay instructions.

### T098 — Rehearse upgrades and full restoration

- [ ] **Complete T098**
- **Dependencies:** T095,T097,T018a. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `scripts/restore-check.ts`, `docs/runbooks/restore.md`, `docs/runbooks/rollback.md`, `apps/api/tests/integration/migration-compatibility.test.ts`, `docs/verification/T098.md`.
- **Acceptance:** Empty/previous-release migrations pass; paired DB/file restore retains references and applies deletion ledger; previous client/API compatibility is checked.
- **Verify (DB+OPS):** Restore to isolated environment, reconcile counts/checksums, test four-role login and submitted files; measure actual RPO/RTO and rollback duration.

### T099 — Close integrated regression and review findings

- [ ] **Complete T099**
- **Dependencies:** T092,T093,T094,T095,T096,T098. **Requirements:** R01–R12. **Estimated scope:** M.
- **Files:** `docs/verification/T099.md`, `tasks/todo.md`, `docs/decisions/0007-release-readiness.md`.
- **Acceptance:** All requirement IDs map to passing evidence; no unresolved P0/P1, tenant leak or silent-loss issue; required policy questions are resolved.
- **Verify (C+I+W+N):** Run affected full suites/build/contracts/native candidate and review correctness, architecture/security/performance; record residual limitations and owner.

**Checkpoint P09.3 — through T099**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P09 exit gate:** The integrated candidate meets safety, usability, load and restore gates with evidence. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P10 — Hosted release candidate and pilot preparation

**Exit outcome:** A recoverable hosted candidate, trained school contacts and native beta builds are ready for real-data pilot.

### T100 — Provision production and verify environment separation

- [ ] **Complete T100**
- **Dependencies:** T099,T018a. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `infra/deploy.yaml`, `infra/reverse-proxy.conf`, `.env.example`, `docs/runbooks/deploy.md`, `docs/verification/T100.md`.
- **Acceptance:** Extend proven synthetic staging infrastructure with approved separate production API/worker/DB/buckets/secrets/email/push, least privileges, TLS and backups; production data never enters staging/preview.
- **Verify (OPS):** Compare configuration with approved inventory; test readiness, private storage, staging email allowlist and backup schedule.

### T101 — Complete production promotion and rollback

- [ ] **Complete T101**
- **Dependencies:** T100,T017,T018a. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `.github/workflows/staging.yml`, `.github/workflows/release.yml`, `docs/runbooks/deploy.md`, `docs/runbooks/rollback.md`, `docs/verification/T101.md`.
- **Acceptance:** Extend the early staging pipeline to promote tested immutable artifacts with serialized migrations, smoke checks and restricted production authorization; flags have owner/expiry.
- **Verify (OPS):** Deploy staging, intentionally fail smoke, exercise previous-image rollback with compatible schema and verify retained data.

### T102 — Produce guides and support procedures

- [ ] **Complete T102**
- **Dependencies:** T051,T057,T069,T081,T099. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `docs/user-guides/admin.md`, `docs/user-guides/teacher.md`, `docs/user-guides/student.md`, `docs/user-guides/parent.md`, `docs/runbooks/onboarding.md`.
- **Acceptance:** Role guides cover actual flows, common failures/privacy/offline limits; named school contact can reconcile import and escalate incident.
- **Verify (DOC+W+N):** Have representative users follow guides using synthetic accounts; correct unclear steps and capture short demonstrations without pupil data.

**Checkpoint P10.1 — through T102**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T103 — Prepare store identities and disclosures

- [ ] **Complete T103**
- **Dependencies:** T094,T097. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `apps/mobile/app.config.ts`, `apps/mobile/eas.json`, `docs/runbooks/store-release.md`, `docs/privacy/store-disclosures.md`, `docs/verification/T103.md`.
- **Acceptance:** Operator-owned signing/accounts, bundle IDs, privacy/support/deletion URLs and truthful data disclosures are prepared; review accounts are synthetic.
- **Verify (DOC+N):** Validate metadata against actual SDK data collection, permission text and account deletion route; confirm device/store requirements from current official docs.

### T104 — Build and distribute native beta candidates

- [ ] **Complete T104**
- **Dependencies:** T101,T103. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `.github/workflows/mobile-build.yml`, `apps/mobile/eas.json`, `tests/e2e/mobile/release-smoke.yaml`, `docs/verification/T104.md`.
- **Acceptance:** Versioned Android internal-testing and iOS TestFlight builds use production-candidate configuration; no debug secrets or staging mixup.
- **Verify (N+OPS):** Install distributed binaries on physical devices; complete login/OTP/push/deep link/upload/offline smoke; record build IDs and compatible runtime.

### T105 — Rehearse pilot onboarding and go/no-go

- [ ] **Complete T105**
- **Dependencies:** T102,T104,T098. **Requirements:** R01–R12. **Estimated scope:** M.
- **Files:** `docs/runbooks/onboarding.md`, `docs/verification/T105.md`, `docs/decisions/0007-release-readiness.md`, `tasks/todo.md`.
- **Acceptance:** Synthetic full-school import and all-role smoke succeed; support/backup/incident owners and school policy approvals are recorded before real records.
- **Verify (OPS+W+N):** Run clean-school rehearsal, verify import counts and guardian review, restore readiness and release checklist; obtain actual pilot go/no-go.

**Checkpoint P10.2 — through T105**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P10 exit gate:** A recoverable hosted candidate, trained school contacts and native beta builds are ready for real-data pilot. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## P11 — Pilot operation, public release and handover

**Exit outcome:** Observed school use validates the product; fixes and acceptance precede public release.

### T106 — Onboard the first pilot school

- [ ] **Complete T106**
- **Dependencies:** T105. **Requirements:** R02,R12. **Estimated scope:** S.
- **Files:** `docs/verification/T106.md`, `docs/runbooks/onboarding.md`.
- **Acceptance:** Authorized staff review source data, import counts, teacher scopes and each guardian link; trained users have working principal flows.
- **Verify (OPS):** School signs off roster reconciliation; supervised attendance/homework smoke uses only approved records; support watches first school morning.

### T107 — Observe pilot and expand to the second school

- [ ] **Complete T107**
- **Dependencies:** T106. **Requirements:** R03–R12. **Estimated scope:** S.
- **Files:** `docs/verification/T107.md`, `docs/runbooks/incidents.md`.
- **Acceptance:** Ten-school-day pilot records task success, marking coverage, errors/crashes and support themes; second school added only after first stabilizes.
- **Verify (OPS):** Measure documented denominators and periods; verify tenant separation with both schools active and review failed jobs daily.

### T108 — Resolve pilot defects with regression evidence

- [ ] **Complete T108**
- **Dependencies:** T107. **Requirements:** R01–R12. **Estimated scope:** M.
- **Files:** `tasks/todo.md`, `docs/verification/T108.md`, `docs/decisions/0007-release-readiness.md`.
- **Acceptance:** Every P0/P1 fixed before release; each actual defect gets its own scoped task/files and failing regression, not a blanket rewrite.
- **Verify (C+I+W+N):** Reproduce→fix→focused regression→affected suites→staging/device check; obtain confirmation from reporting school and update acceptance evidence.

**Checkpoint P11.1 — through T108**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

### T109 — Approve and submit the public MVP

- [ ] **Complete T109**
- **Dependencies:** T108,T104. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `docs/runbooks/store-release.md`, `docs/verification/T109.md`, `CHANGELOG.md`.
- **Acceptance:** Product/school acceptance and release authorization are recorded; privacy/support/store materials match final candidate; signed builds are submitted.
- **Verify (OPS+N):** Re-run candidate smoke after any fixes; record release tag/image/native build IDs and submission outcomes without claiming store approval prematurely.

### T110 — Verify public availability and monitor rollout

- [ ] **Complete T110**
- **Dependencies:** T109. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `docs/verification/T110.md`, `docs/runbooks/incidents.md`, `docs/runbooks/rollback.md`.
- **Acceptance:** Track store review to actual availability, respond to review issues, stage school rollout and observe first hour/morning/week.
- **Verify (OPS+N):** Install approved public binaries, test primary flows, watch agreed thresholds and exercise kill switch/rollback if triggered; preserve user data.

### T111 — Complete handover and next-release backlog

- [ ] **Complete T111**
- **Dependencies:** T110. **Requirements:** R12. **Estimated scope:** M.
- **Files:** `README.md`, `docs/runbooks/ownership.md`, `docs/verification/T111.md`, `tasks/todo.md`, `CHANGELOG.md`.
- **Acceptance:** Named maintainers own support/backups/keys/dependencies; expired flags cleaned up; deferred roadmap modules remain separately scoped.
- **Verify (DOC+OPS):** Handover operator executes restore/job triage walkthrough; record acceptance, unresolved lower-priority defects and next-module discovery tasks.

**Checkpoint P11.2 — through T111**

- [ ] Tasks above meet acceptance; affected verification is recorded; integrated state is working or explicitly identified as a foundational slice.
- [ ] Review permission/data-integrity/error paths and actual UI/device behaviour where applicable; resolve failures before dependent tasks.
- [ ] Update specs/contracts and demonstrate progress for review; keep unresolved decisions visible at the end of both planning files.

**P11 exit gate:** Observed school use validates the product; fixes and acceptance precede public release. Evidence and owner sign-off are recorded; no unresolved phase-blocking decision or required review finding remains.

## Release-wide completion checklist

- [ ] R01–R12 map to delivered screens/endpoints and passing acceptance evidence.
- [ ] No pending P0/P1 defect, tenant/guardian data leak, silent academic-data loss or unmitigated reachable critical/high vulnerability.
- [ ] All four roles finish primary workflows on web, Android and iOS; native admin's web-only boundaries are accepted.
- [ ] All real-data policies, provider ownership, jurisdiction/retention decisions and pilot permissions are settled.
- [ ] Database and immutable files restore together within accepted recovery targets; deletion ledger is reapplied.
- [ ] The latest release candidate passes required CI, device tests, accessibility, performance and security checks.
- [ ] School/product acceptance, support owner, monitoring and rollback readiness are recorded.
- [ ] Store submission and actual store availability are distinguished; public binaries are smoke-tested.
- [ ] User guides, release notes, runbooks and operational ownership match the released implementation.
- [ ] Deferred examinations, fees, chat, transport and other roadmap modules have not entered this MVP without an explicit scope revision.

## Open questions and decisions for later review

These are unresolved product/operational decisions, not completed tasks. The proposed defaults allow a comprehensive plan now; resolve each before the affected implementation or real-data release. The complete decision table below is shared with plan.md.


| ID | Question | Proposed default / impact | Resolve before |
|---|---|---|---|
| Q01 | Does MVP mean Month 3 core workflows or include Month 4–6 exams, messaging, events and fees? | Month 3 scope plus production safeguards. Including later modules requires new specs/tasks/estimate. | P00 scope acceptance |
| Q02 | Must all administration work be native, or is web-first administration with native overview acceptable? | Web owns bulk setup/import/timetable editing; all roles have native principal views. | P00 UX acceptance |
| Q03 | Which country/region, school type and data-residency/retention obligations apply? | No jurisdiction inferred from the developer's timezone; school/operator supplies policy. | Infrastructure selection and real data |
| Q04 | Can each student use an individual email address? Who controls younger pupils' accounts/recovery? | Invite-only individual email for self-service; separate student records without accounts. Username/school-ID login needs a designed alternative. | P02 |
| Q05 | May parents submit homework for children, and how is guardian authority verified/revoked? | Parents read only; school staff verifies links. Supporting parent submission changes audit and submission policy. | P02/P03 |
| Q06 | Daily or per-period attendance? What counts toward attendance percentage, and who can correct old dates? | Daily; late counts present; excused excluded; older corrections admin-only. | P04 |
| Q07 | Does “real-time” require live subscriptions or is ≤30-second foreground refresh sufficient? | Bounded refresh; push/inbox for communications. Strict streaming adds delivery/authorization work. | P04 |
| Q08 | Are rotating timetables, substitutions, half days, room scheduling or multiple campuses required? | Weekly schedule, optional room conflict check, one campus; no rotating-week solver. | P03/P05 |
| Q09 | What are late-work, resubmission, grading precision and grade correction rules? | Late accepted/labeled; attempts retained; explicit release/reopen; no rank/report cards. | P07 |
| Q10 | Are offline notices/timetables permitted on shared devices with a 24-hour stale-access window? | Opt-in restricted cache; disable if school rejects residual offline access. | P08 |
| Q11 | Is email verification sufficient, or is SMS OTP mandatory? What notification quiet hours are needed? | Email OTP, native push and durable inbox; SMS deferred, generic lock-screen text. | P02/provider configuration |
| Q12 | What team, budget, hosting vendor and support hours are available? | Two engineers plus part-time QA/design; managed services; named school-day support owner. | P00/P01 and purchases |
| Q13 | Which Android/iOS versions, languages and accessibility needs do pilot users have? | English, SDK-supported devices, accessible UI from first slice; survey before version lock. | P01 |
| Q14 | Who owns Apple/Google/Expo/domain/provider accounts and store review credentials? | School/operator-owned accounts with least-privilege developer access; no personal ownership assumed. | P01/P10 |
| Q15 | What are permitted file types, quotas and educational-record/audit/backup retention periods? | Images/PDF, 10 MiB, five files; orphan 24 h; core record retention needs policy. | P06 and real data |
| Q16 | What source spreadsheets exist, and how are admission numbers/duplicates/withdrawals represented? | Explicit CSV templates and previewed import; no inferred guardian access. | P03 |
| Q17 | Are the proposed recovery and availability targets sufficient? | RPO 24 h/RTO 4 h, 99.5% initial reliability; stronger targets change hosting cost/operations. | P09/P10 |
| Q18 | Who accepts each milestone and the pilot, and are 14–20 weeks compatible with the intended launch? | Named product owner plus pilot-school representative; re-estimate after first working slice. | P00 |
| Q19 | Should the plan receive a separate external/cross-model architecture review before implementation? | Optional review recorded here for later consideration; no external CLI or service invoked. | Architecture acceptance |


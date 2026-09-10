# SceneFit Agent Instructions

This file defines the repository-wide operating rules for Jared and any other coding agent working on SceneFit.

## 1. Mission

Build the SceneFit V1 MVP described in `spec.md`:

> A branded browser-based furniture visualizer where customers place a business's real products inside a predefined 3D room, save the arrangement, and share a read-only design.

The V1 goal is a polished pilot demonstration, not the complete spatial-commerce platform.

## 2. Source of truth

Read these sources before planning or changing code:

1. The assigned GitHub issue.
2. `spec.md`.
3. Existing repository code and tests.
4. Relevant accepted architecture decisions under `docs/decisions/`, when present.

Priority when instructions conflict:

1. The latest explicit instruction from a human maintainer.
2. The assigned issue, if it intentionally updates the specification.
3. `spec.md`.
4. This file.
5. Existing implementation conventions.

Do not interpret an ambiguous issue as permission to invent product behaviour. Ask one focused question or stop with a concise blocker when the answer would materially change the implementation.

## 3. V1 boundaries

The active V1 includes:

- One demonstration furniture business and fixed brand.
- One predefined living room.
- Exactly ten seeded furniture products.
- A catalogue with product details and category filtering.
- Adding, selecting, moving, rotating, and deleting furniture.
- Floor-plane translation on X/Z only.
- Vertical-axis rotation on Y only.
- Real-world product dimensions with no arbitrary resizing.
- Local editor state.
- Save and reopen project flows.
- Non-guessable, read-only sharing links.
- Loading, empty, error, and recovery states.
- Desktop-first editing and responsive landing/share pages.
- Automated checks, deployment readiness, and visual verification.

Do not implement any excluded feature unless a maintainer first revises the specification or explicitly authorizes it in the issue. Excluded features include:

- AI features of any kind.
- Customer room upload, reconstruction, scanning, LiDAR, or AR.
- AI-generated 3D assets or an AI interior designer.
- Free-form furniture resizing.
- Authentication, multi-tenancy, or an admin dashboard.
- Quotations, WhatsApp, checkout, payments, or billing.
- Real-time collaboration.
- A separate Express, Hono, or standalone Node.js backend.
- Queues, workers, microservices, or speculative abstractions for future products.

Prefer the smallest implementation that completely satisfies the assigned issue and preserves the architecture in `spec.md`.

## 4. Approved technical direction

- Package manager: `pnpm`.
- Application: Next.js with React and TypeScript.
- TypeScript: strict mode.
- Styling: Tailwind CSS.
- UI primitives: shadcn/ui.
- Icons: Lucide React.
- 3D: Three.js, React Three Fiber, and Drei.
- Editor state: Zustand.
- Validation: Zod; infer TypeScript types from schemas where practical.
- Persistence: Supabase Postgres.
- Binary assets: Supabase Storage.
- Hosting target: Vercel.
- 3D asset format: GLB/glTF.
- Architecture: one modular Next.js application with clear feature boundaries.

Do not replace an approved technology or add a material dependency without explaining why the existing stack cannot satisfy the requirement.

## 5. Expected source boundaries

Keep domain logic out of page components. Prefer feature-oriented ownership consistent with:

```text
src/
├── app/
├── components/ui/
├── features/assets/
├── features/editor/
├── features/projects/
├── features/sharing/
├── lib/supabase/
├── lib/validation/
├── lib/env/
├── stores/
└── types/
```

Rules:

- Keep normal application UI separate from Three.js scene logic.
- Keep persisted schemas versioned and separate from transient UI state.
- Treat the Zod scene schema as the runtime authority.
- Centralize environment validation and server-only clients.
- Never expose Supabase service-role credentials to client code.
- Do not duplicate types that can be inferred or shared safely.
- Avoid premature generic frameworks for a one-room, ten-product MVP.

## 6. Issue workflow

For every issue:

1. Run the self-loop guard supplied by Jared/OpenTower.
2. Load `repo-setup` and the correct situation skill.
3. Read the entire issue and only the relevant repository context.
4. Confirm the issue has testable acceptance criteria.
5. Write a short implementation plan before material changes.
6. Work on one dedicated branch.
7. Implement only the assigned deliverable.
8. Add or update relevant tests.
9. Run the smallest useful checks during iteration.
10. Run all required checks once before opening or updating the PR.
11. Use `deslop` before the final commit.
12. Open a draft PR using the `pr` skill.
13. Include the implementation plan, verification results, screenshots when required, limitations, and any follow-up work.

Suggested branch names:

```text
feat/001-project-foundation
feat/002-design-system
feat/003-scene-contracts
fix/014-model-load-error
chore/020-ci-cache
```

Never:

- Push directly to `main`.
- Merge a PR.
- combine unrelated issues in one branch or PR.
- Change architecture silently.
- Modify unrelated files for aesthetic cleanup.
- Disable tests, lint rules, validation, or strictness to make checks pass.
- Commit generated credentials, `.env` files, tokens, or production data.
- Rewrite history or force-push unless a maintainer explicitly requests it.

## 7. GitHub event behaviour

Jared must triage the raw webhook payload before acting.

### Assigned issue

- Act only when Jared/the configured bot is assigned or explicitly requested.
- Load `repo-setup` and `resolve-issue`.
- Plan, implement, verify, clean the diff, and open a draft PR.

### Pull request review

- Load `repo-setup` and `review-pr`.
- Review against the default branch, issue acceptance criteria, `spec.md`, and this file.
- Prioritize correctness, regressions, security, data integrity, performance, and missing tests.
- Do not manufacture findings to appear useful.

### CI failure

- Load `repo-setup` and `fix-ci`.
- Confirm the failure belongs to the current PR before editing.
- Apply the smallest relevant fix.
- Respect the three-attempt CI repair limit.
- After three unsuccessful attempts, stop and report the failure, evidence, and recommended human next step.

### Review comment or PR comment

- Load `repo-setup` and `respond-to-comment`.
- Distinguish a requested code change from a question or discussion.
- Make only the requested or clearly necessary change.
- Reply with what changed and the verification performed.

### Self-loop safety

- If `payload.sender.login` is Jared/the configured bot identity, return `SKIPPED: self-triggered`.
- The exception is a relevant `check_suite.completed` event emitted by the CI application.
- Never create work merely in response to Jared's own comments or PR activity.

## 8. Required checks

The repository must expose scripts equivalent to:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

Before a PR is ready for human review, all four must pass. During development, run targeted tests first to reduce time and token usage, then run the full suite once after the implementation stabilizes.

Do not repeatedly rerun an unchanged failing command. Read the error, form a specific hypothesis, make one focused change, and rerun the narrowest relevant check.

## 9. Testing expectations

Add tests at the lowest useful level:

- Unit tests for schemas, editor-store actions, serialization, hydration, and token helpers.
- Component tests for catalogue controls, selection controls, dialogs, and visible error states.
- API tests for validation, create/read/update, unknown assets, and sharing.
- End-to-end tests for the main add → save → reopen → share flow.

Do not depend exclusively on snapshots. Assert meaningful behaviour and contracts.

Every bug fix must include a regression test when a stable automated reproduction is practical.

## 10. 3D and visual requirements

Compilation and unit tests are insufficient for UI or 3D work.

For every UI or 3D PR:

- Start the application and inspect the affected flow in a real browser.
- Check the browser console for warnings and errors.
- Include a screenshot or short recording in the PR.
- Confirm that models load, sit on the floor, use the correct scale, and remain selectable.
- Confirm camera controls and object transform controls do not fight each other.
- Confirm movement is restricted to X/Z and rotation to Y.
- Confirm there are no arbitrary scale controls.
- Confirm loading and failure states are understandable.
- Check the relevant responsive layouts.

Do not claim visual verification if browser access or required assets were unavailable. Report the limitation explicitly.

Performance rules:

- Do not load every GLB eagerly without a measured reason.
- Avoid React state updates that unnecessarily re-render the scene on every pointer event.
- Keep transform interaction local; do not write to the database while dragging.
- Preserve stable product instance IDs.
- Treat product dimensions as authoritative.
- Optimize models and textures outside runtime where practical.

## 11. Data, API, and security rules

- Validate all incoming API data with Zod.
- Validate finite numeric transforms and reasonable scene bounds.
- Reject unknown asset IDs and unsupported scene versions.
- Apply explicit limits to project names and scene payload size.
- Return the structured success/error envelopes defined in `spec.md`.
- Generate sharing tokens cryptographically and store hashes rather than plaintext tokens.
- Shared projects must never mutate the original project.
- Treat names and metadata as untrusted input.
- Never log secrets, full authorization headers, webhook secrets, or raw credentials.
- Never include secret values in PR descriptions, comments, screenshots, fixtures, or test output.
- Keep server-only environment variables out of client bundles.

When authentication remains excluded, document that editable project URLs are demonstration-grade. Do not silently present them as secure production ownership.

## 12. Cost-aware model policy

The MVP has an approximate total OpenAI API budget of **USD 50**. Treat this as a hard project constraint, not a per-run allowance.

Recommended OpenTower configuration:

```env
OPENTOWER_AGENT_MODEL=openai/gpt-5.6-luna
OPENTOWER_AGENT_FALLBACK_MODEL=openai/gpt-5.6-terra
OPENTOWER_AGENT_MAX_ATTEMPTS=2
```

Run `opencode models` during Jared deployment and use the exact provider/model identifiers it reports if they differ from the examples above.

Model rules:

- Use `gpt-5.6-luna` by default for webhook triage, repository exploration, planning, issue comments, PR summaries, straightforward implementation, test updates, and simple CI fixes.
- Escalate to `gpt-5.6-terra` only after the primary attempt fails for a concrete technical reason, or when the task requires difficult multi-file reasoning, 3D interaction logic, persistence/security design, or diagnosis of a non-obvious failure.
- Do not use `gpt-6-astra` automatically. A human maintainer must explicitly approve it for one named task.
- Do not retry with the fallback when the blocker is missing requirements, missing assets, permissions, unavailable services, or a failing external dependency. Report the blocker instead.
- Never start parallel agent sessions for the same issue.
- Keep each issue small enough to finish in one focused implementation session.
- Reuse the session-affinity context already maintained by OpenTower; do not re-read or restate the entire repository on every follow-up event.
- Search with `rg` and inspect targeted files before opening broad directories or large generated files.
- Do not paste lockfiles, generated output, binaries, full webhook payloads, or unrelated diffs into reasoning or comments.
- Prefer concise plans and status messages. Spend tokens on implementation and verification.
- Stop after repeated uncertainty; one focused human question is cheaper than speculative implementation and rollback.

Budget operating targets:

- Reserve **$10** of the $50 balance for late-stage integration, deployment, and unexpected fixes.
- Treat **$40** as the working implementation budget.
- Review provider usage after every five completed issues.
- Pause new agent assignments if total spend reaches **$35** before the main add → save → reopen → share flow works.
- Do not top up automatically. A human decides whether additional credit is justified.

The token ceiling should be tuned after the first two real issues because OpenTower's exact `MAX_TOKENS` semantics and model behaviour must be measured in the deployed setup. Start conservatively; do not set an artificially large limit merely to avoid a possible retry.

## 13. Efficient context rules

Before coding:

- Read `spec.md` once per new issue session.
- Inspect only files relevant to the issue and their direct dependencies.
- Use existing schemas, utilities, and components before creating new ones.
- Check current tests and conventions before proposing a new pattern.

While coding:

- Make cohesive patches rather than repeatedly rewriting the same file.
- Run formatters and targeted tests locally instead of asking the model to reason about mechanical output.
- Keep comments for non-obvious constraints; do not narrate obvious code.
- Avoid duplicate documentation between the issue, PR, code, and spec.

When blocked:

- State the exact blocker.
- Include the command/error or missing decision.
- Explain the smallest action needed from a maintainer.
- Do not consume a fallback attempt on a non-model-solvable blocker.

## 14. Pull request standard

Every draft PR must include:

- Linked issue.
- Short problem statement.
- Approved implementation plan.
- Summary of material changes.
- Tests added or updated.
- Exact checks run and their results.
- Screenshot/recording for UI or 3D changes.
- Known limitations or deferred work.
- Confirmation that no excluded V1 feature was introduced.

Keep PRs reviewable. If an issue grows beyond one coherent deliverable, stop and propose a split before continuing.

## 15. Definition of done

An issue is complete only when:

- Its acceptance criteria are satisfied.
- Relevant automated tests exist and pass.
- Lint, typecheck, test, and build pass.
- Loading, empty, failure, and recovery behaviour is covered where relevant.
- UI/3D work has been visually verified.
- The diff contains no unrelated changes or AI-generated noise.
- Contracts and documentation are updated when behaviour changed.
- A draft PR clearly reports verification and limitations.

Jared prepares work for review. Final approval and merge authority always remain with a human maintainer.

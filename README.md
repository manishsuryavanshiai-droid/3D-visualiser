# SceneFit

**See it in your space.**

SceneFit is a browser-based 3D furniture visualizer for furniture retailers. Customers can browse a real product catalogue, place accurately scaled furniture inside a predefined room, arrange the scene, save their design, and share a read-only version.

## Project status

SceneFit is currently in the V1 MVP planning and foundation stage.

The first release is intentionally limited to:

- One demonstration furniture business
- One predefined 3D living room
- Ten seeded furniture products
- Product catalogue and category filtering
- Add, select, move, rotate, and delete interactions
- Save and reopen projects
- Non-guessable, read-only sharing links
- Desktop-first editing

AI room reconstruction, room scanning, AR, quotations, payments, authentication, admin tooling, and multi-tenancy are outside V1.

## MVP technology

| Area | Technology |
| --- | --- |
| Application | Next.js, React, TypeScript |
| Interface | Tailwind CSS, shadcn/ui, Lucide React |
| 3D | Three.js, React Three Fiber, Drei |
| Editor state | Zustand |
| Validation | Zod |
| Database | Supabase Postgres |
| Asset storage | Supabase Storage |
| Hosting | Vercel |
| Package manager | pnpm |

SceneFit uses a modular-monolith architecture: one deployable Next.js application with separate customer UI, 3D editor, editor-state, project-persistence, and sharing modules.

## Documentation

- [`spec.md`](./spec.md) — product requirements, architecture, contracts, phases, and Definition of Done
- [`AGENTS.md`](./AGENTS.md) — repository rules for Jared and other coding agents
- [`assets/scenefit-v1-architecture.png`](./assets/scenefit-v1-architecture.png) — V1 architecture diagram

The assigned GitHub issue and `spec.md` are the primary implementation sources of truth. Agents must read `AGENTS.md` before making changes.

## Development workflow

SceneFit follows an issue-driven workflow:

1. Define one coherent GitHub issue with acceptance criteria and exclusions.
2. Assign the issue to Jared.
3. Jared plans and implements it on a dedicated branch.
4. Jared opens a draft pull request and reports verification results.
5. Required CI checks and visual verification are completed.
6. A human maintainer reviews and merges the pull request.

Agents must not push directly to `main` or merge their own pull requests.

## Required checks

Once the application foundation has been created, the repository must provide:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

All required checks must pass before a pull request is ready to merge. UI and 3D pull requests must also include browser-based visual verification.

## Local development

Local setup commands will be added by the repository-foundation issue after the Next.js application exists. Do not invent setup steps before the project scaffold and environment contract are committed.

## Security

- Never commit `.env` files, API keys, tokens, or service-role credentials.
- Privileged Supabase writes must remain behind the server boundary.
- Shared projects are read-only.
- Editable project URLs in V1 are demonstration-grade until project ownership or another edit-access mechanism is implemented.

## Repository ownership

Final architectural approval and merge authority remain with the human maintainers. Jared prepares scoped, tested pull requests but does not merge them.

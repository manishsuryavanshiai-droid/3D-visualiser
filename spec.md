# SceneFit — MVP Product and Technical Specification

**Status:** Draft v0.3  
**Audience:** Founders, product owner, Jared coding agent, reviewers  
**Product name:** SceneFit  
**Tagline:** See it in your space.  
**Repository name:** `scenefit`  
**Target vertical:** Furniture retail  
**Architecture:** Modular monolith  

---

## 1. Executive Summary

SceneFit is a browser-based furniture visualizer for one demonstration furniture business. A customer opens a branded experience, browses a catalogue of ten products, places products inside one predefined 3D living room, moves and rotates them, saves the design, reopens it later, and creates a read-only sharing link.

The MVP is intended to answer one business question:

> Will furniture businesses and their customers find a simple branded 3D visualizer valuable enough to pilot and eventually pay for?

The MVP is not the complete spatial-commerce platform. It deliberately excludes AI room reconstruction, customer room scanning, augmented reality, AI-generated 3D models, quotations, WhatsApp, payments, business administration, authentication, and multi-tenancy.

---

## 2. Problem

Furniture customers struggle to imagine how a product will look and fit inside a room. Product photos and showroom displays do not provide enough spatial confidence. This uncertainty can delay purchases, increase reliance on salespeople, and cause customers to abandon otherwise suitable products.

Medium-sized furniture businesses often cannot justify building a custom IKEA-style visualization system. The proposed product gives them a branded digital showroom built on shared platform technology.

---

## 3. Product Goal

Build the smallest polished experience that allows a furniture-business prospect to understand the long-term value of spatial commerce.

### Primary goal

Demonstrate a smooth end-to-end flow:

1. Open the branded visualizer.
2. Enter the predefined living room.
3. Browse ten furniture products.
4. Add products to the room.
5. Select, move, rotate, and delete placed products.
6. Save the design.
7. Reopen the saved design.
8. Create and open a read-only sharing link.

### Secondary goals

- Demonstrate accurate product scale using business-provided dimensions.
- Validate browser performance with optimized GLB assets.
- Create a technical foundation that can later support tenants, AI workers, quotations, and AR without including those systems now.

---

## 4. Success Criteria

The MVP is successful when all of the following are true:

- A first-time user can place their first product within 30 seconds of opening the editor.
- A user can complete the main design flow without instructions from a developer.
- The editor maintains interactive performance on the supported reference devices.
- Saved projects reopen with the same products, positions, and rotations.
- A read-only shared project cannot modify the original project.
- At least one real furniture business agrees to review or pilot the demo.
- Feedback is collected from at least five furniture-business stakeholders or salespeople.

These are validation targets, not analytics requirements for the first coding milestone.

---

## 5. Users

### 5.1 Primary user: Furniture customer

The customer wants to explore combinations of furniture inside a representative living room before contacting the business.

### 5.2 Secondary user: Furniture salesperson

The salesperson wants to use the visualizer during a conversation to demonstrate combinations and share a design with a customer.

### 5.3 Internal technical operator

For V1, initial products and models are seeded manually by the development team. There is no admin portal.

---

## 6. V1 Scope

### 6.1 Included

- One demonstration furniture business.
- One fixed brand configuration.
- One predefined 3D living room.
- Exactly ten seeded furniture products.
- Product catalogue with categories and thumbnails.
- Product-detail information required for selection.
- 3D GLB/glTF product models.
- Accurate product dimensions.
- Add a product to the room.
- Select a placed product.
- Move a selected product along the floor plane.
- Rotate a selected product around the vertical axis.
- Delete a selected product.
- Reset the design with confirmation.
- Save a project.
- Reopen a saved project.
- Create a non-guessable sharing link.
- Read-only shared-project view.
- Loading, empty, failure, and recovery states.
- Desktop-first responsive interface.
- Basic automated tests and CI checks.

### 6.2 Explicitly excluded

- Customer room-photo upload.
- AI-assisted room reconstruction.
- Room scanning.
- LiDAR integration.
- Augmented reality.
- AI-generated furniture models.
- AI interior designer.
- Free-form product resizing.
- Quotations.
- WhatsApp integration.
- Checkout or payments.
- Customer or employee authentication.
- Admin dashboard.
- Catalogue import interface.
- Multiple businesses or tenants.
- Custom domains.
- Event-management workflows.
- Real-estate workflows.
- Dedicated Express, Hono, or standalone Node.js API.
- Job queues or background AI workers.
- Real-time collaboration.
- Comments, likes, or social features.
- Production billing and subscription management.

Excluded functionality must not be implemented speculatively.

---

## 7. Product Rules

1. Real products retain their real-world dimensions.
2. Users cannot arbitrarily resize furniture.
3. Move operations are constrained to the floor plane.
4. Rotation is constrained to the vertical axis in V1.
5. A newly added product must appear in a valid, visible location.
6. Products must not be placed below the floor.
7. Collision prevention is not required in V1, but grossly invalid placement should be avoided.
8. Shared projects are read-only.
9. Editing occurs locally for responsiveness; the server is not called on every transform change.
10. The server validates all persisted scene data.
11. Storage paths and database writes must not expose privileged credentials to the browser.
12. The application must present unsupported or failed model loads gracefully.

---

## 8. Primary User Flow

### 8.1 Start a design

1. User opens the landing page.
2. User sees the demo business branding and primary call to action.
3. User selects **Design your room**.
4. The design page loads the catalogue and predefined room.
5. A progress state is shown while 3D assets load.
6. The editor becomes interactive when minimum required assets are ready.

### 8.2 Add and arrange products

1. User browses or filters the catalogue.
2. User selects a product card.
3. User chooses **Add to room**.
4. A new product instance appears in the room.
5. The new instance becomes selected.
6. User moves or rotates the selected product.
7. User may add multiple instances of a product unless the product metadata forbids it.
8. User may delete a selected instance.

### 8.3 Save a design

1. User selects **Save**.
2. If the project has not been saved before, the user enters a project name and optional customer name.
3. Client state is serialized into the scene-data contract.
4. Server validates and persists the project.
5. UI confirms success and updates the URL to the saved project.
6. Save failure preserves local work and allows retry.

### 8.4 Reopen a design

1. User opens `/projects/[id]`.
2. Server retrieves the project.
3. Client loads required assets.
4. Product instances are recreated from saved scene data.
5. User continues editing.

### 8.5 Share a design

1. User saves the project.
2. User selects **Share**.
3. Server creates or returns a non-guessable share token.
4. UI displays a copyable `/share/[token]` URL.
5. Recipient opens the shared URL.
6. Recipient can inspect and navigate the scene but cannot edit or save over the project.

---

## 9. Pages and Routes

### `/`

Branded landing page.

Required content:

- Business logo/name.
- Short value proposition.
- Preview of the 3D visualizer.
- Primary **Design your room** call to action.
- Clear indication that this is a demonstration.

### `/design`

New-project editor.

### `/projects/[id]`

Editable saved project.

### `/share/[token]`

Read-only project experience.

### API routes

- `GET /api/assets`
- `POST /api/projects`
- `GET /api/projects/[id]`
- `PUT /api/projects/[id]`
- `POST /api/projects/[id]/share`
- `GET /api/shared/[token]`

Exact implementation may use Next.js route handlers or server actions, but public contracts and validation behaviour must remain explicit and tested.

---

## 10. Editor Layout

Desktop layout contains:

- Top navigation/header.
- Left catalogue panel.
- Central 3D canvas.
- Right selected-object inspector or compact contextual panel.
- Save and Share actions.
- Loading and error overlays.

### Catalogue panel

- Product thumbnail.
- Product name.
- Category.
- Price in AED.
- Dimensions.
- **Add to room** action.
- Basic category filtering.

Search is optional for ten products and should only be added if it does not delay the main editor.

### Selected-object controls

- Product name.
- Price.
- Dimensions.
- Move mode.
- Rotate mode.
- Delete action.
- Clear visible indication of selection.

### Canvas controls

- Orbit/pan/zoom camera controls appropriate for desktop.
- Prevent camera from moving below the floor where practical.
- Reset-camera action.
- Clear interaction feedback.

### Mobile behaviour

The landing and shared pages must be responsive. The editor should remain viewable on mobile, but precise mobile editing is not a V1 acceptance requirement. On small screens, panels may become drawers or sheets.

---

## 11. 3D Requirements

### 11.1 Technology

- Three.js.
- React Three Fiber.
- Drei helpers where appropriate.
- GLB/glTF asset format.

### 11.2 Room

- One optimized, predefined living-room scene.
- Floor is the authoritative placement plane.
- Room includes suitable lighting or receives application-level lighting.
- Room geometry is not editable.

### 11.3 Product models

- Ten optimized GLB models.
- Model origin and pivot must support floor placement and vertical-axis rotation.
- Product dimensions in the database are the source of truth.
- Visual model scale must match stored dimensions.
- Models must not depend on local filesystem paths in production.
- Missing models display a non-blocking failure state or placeholder.

### 11.4 Transform behaviour

- Translation on X/Z floor axes only.
- Rotation around Y axis only.
- No arbitrary scale handles.
- Product instance identity remains stable during edits and persistence.
- Transform changes update client state immediately.

### 11.5 Performance targets

- Initial page shell should display before all 3D assets finish loading.
- Required models use lazy loading where practical.
- Visible loading progress is required.
- Models and textures must be compressed/optimized before production use.
- Avoid unnecessary React re-renders on every pointer movement.
- Maintain an interactively usable frame rate on the agreed reference laptop.

Exact numeric asset budgets should be finalized after inspecting the selected room and product models.

---

## 12. Technical Architecture

![SceneFit V1 MVP architecture](./assets/scenefit-v1-architecture.png)

**Figure 1 — V1 MVP architecture.** The diagram shows the customer journey, modular Next.js application, 3D editor, local editor state, server layer, Supabase persistence and storage, Vercel deployment boundary, and functionality deferred beyond V1. The diagram was created before the product name was finalized; “Spatial Commerce” in its heading refers to **SceneFit**.

### 12.1 Application

- Next.js.
- React.
- TypeScript in strict mode.
- Tailwind CSS.
- shadcn/ui.
- Lucide React icons.

### 12.2 Client state

- Zustand for editor state.
- Local transform updates.
- Dirty/unsaved state tracking.
- Server persistence only on Save or controlled autosave.

Autosave is optional for the first implementation. If included, it must be debounced and must not interfere with manual Save.

### 12.3 Validation

- Zod schemas for API inputs, scene data, and important environment variables.
- TypeScript types should be inferred from schemas where practical to avoid contract drift.

### 12.4 Persistence

- Supabase Postgres for asset metadata, projects, and share tokens.
- Supabase Storage for GLB files, thumbnails, textures, and the room model.
- Next.js server routes form the privileged write boundary.
- Service-role credentials must never be exposed in client bundles.

### 12.5 Hosting

- Vercel for the Next.js application.
- Supabase-hosted database and object storage.
- Preview deployments for pull requests where configured.

### 12.6 V1 architecture principle

Use a modular monolith:

> One deployable Next.js application with clear internal feature boundaries.

Do not create a separate backend service until long-running AI/3D jobs, integrations, or scale requirements justify it.

---

## 13. Suggested Source Structure

```text
src/
├── app/
│   ├── page.tsx
│   ├── design/page.tsx
│   ├── projects/[id]/page.tsx
│   ├── share/[token]/page.tsx
│   └── api/
│       ├── assets/route.ts
│       ├── projects/route.ts
│       ├── projects/[id]/route.ts
│       ├── projects/[id]/share/route.ts
│       └── shared/[token]/route.ts
├── components/
│   └── ui/
├── features/
│   ├── assets/
│   ├── editor/
│   ├── projects/
│   └── sharing/
├── lib/
│   ├── supabase/
│   ├── validation/
│   └── env/
├── stores/
│   └── editor-store.ts
└── types/
```

The agent may refine file names, but it must preserve feature separation and avoid dumping domain logic into page components.

---

## 14. Data Model

### 14.1 `assets`

| Field | Type | Notes |
| --- | --- | --- |
| `id` | UUID | Primary key |
| `name` | Text | Required |
| `category` | Text | Required |
| `sku` | Text | Unique for demo business |
| `description` | Text | Optional |
| `price_aed` | Integer | Store minor units if decimals are needed |
| `thumbnail_url` | Text | Required |
| `model_url` | Text | Required |
| `width_m` | Numeric | Positive, authoritative |
| `height_m` | Numeric | Positive, authoritative |
| `depth_m` | Numeric | Positive, authoritative |
| `metadata` | JSONB | Optional controlled metadata |
| `created_at` | Timestamp | Server generated |

### 14.2 `projects`

| Field | Type | Notes |
| --- | --- | --- |
| `id` | UUID | Primary key |
| `name` | Text | Required |
| `customer_name` | Text | Optional |
| `scene_data` | JSONB | Validated versioned scene |
| `created_at` | Timestamp | Server generated |
| `updated_at` | Timestamp | Server updated |

### 14.3 `project_shares`

| Field | Type | Notes |
| --- | --- | --- |
| `id` | UUID | Primary key |
| `project_id` | UUID | Foreign key |
| `token_hash` | Text | Prefer storing a hash rather than plaintext token |
| `created_at` | Timestamp | Server generated |
| `expires_at` | Timestamp | Nullable for demo |

### 14.4 Scene-data contract

```ts
type SceneDataV1 = {
  version: 1;
  roomId: string;
  placedAssets: Array<{
    instanceId: string;
    assetId: string;
    position: [number, number, number];
    rotation: [number, number, number];
  }>;
  camera?: {
    position: [number, number, number];
    target: [number, number, number];
  };
};
```

The Zod schema is authoritative. Invalid or unsupported scene versions must return a structured error rather than being partially accepted.

---

## 15. API Behaviour

### Success response

```json
{
  "data": {}
}
```

### Error response

```json
{
  "error": {
    "code": "validation_failed",
    "message": "The request could not be processed.",
    "details": {}
  }
}
```

### Required error cases

- `validation_failed` — malformed input.
- `asset_not_found` — unknown asset in scene data.
- `project_not_found` — missing project.
- `share_not_found` — invalid or expired share token.
- `unsupported_scene_version` — unknown scene-data version.
- `storage_unavailable` — required asset could not be loaded.
- `internal_error` — unexpected server failure without leaking secrets.

### Persistence requirements

- Server validates every referenced asset ID before saving where practical.
- Numeric values must be finite and within reasonable bounds.
- Project names have a defined maximum length.
- API must reject oversized scene payloads.
- Share tokens must be cryptographically random and non-guessable.
- Shared-project response must not expose internal token hashes or privileged metadata.

---

## 16. Client State

Minimum editor store responsibilities:

- `projectId`.
- `projectName`.
- `roomId`.
- `assets` catalogue.
- `placedAssets`.
- `selectedInstanceId`.
- active interaction mode.
- dirty/unsaved flag.
- save status.
- asset-loading status.
- editor error state.

Required actions:

- `addAsset`.
- `selectInstance`.
- `updatePosition`.
- `updateRotation`.
- `deleteInstance`.
- `clearScene`.
- `hydrateScene`.
- `markSaved`.

Undo/redo may be included only after core persistence works and must not block MVP completion.

---

## 17. Loading, Empty, and Error States

The implementation must include intentional states for:

- Application shell loading.
- Catalogue loading.
- Room-model loading.
- Product-model loading.
- Empty scene.
- Model load failure.
- Catalogue load failure with retry.
- Save in progress.
- Save success.
- Save failure with retry and preserved client state.
- Project not found.
- Share link not found or expired.
- Unsupported browser/WebGL condition.

No main user action should fail silently.

---

## 18. Accessibility and Interaction

- Standard UI controls must be keyboard accessible.
- Buttons require accessible labels.
- Icon-only actions require tooltips and screen-reader labels.
- Visible focus states are required.
- Colour must not be the only selection indicator.
- Dialogs and drawers should use accessible shadcn/ui primitives.
- The 3D canvas should have an accessible textual description and clear instructions.
- Destructive actions such as reset require confirmation.

Full keyboard manipulation of 3D objects is desirable but not required for initial V1 acceptance.

---

## 19. Security and Privacy

- Never commit API keys or Supabase secrets.
- Validate environment variables at startup.
- Never send service-role credentials to the browser.
- Restrict database and storage permissions to the minimum required.
- Treat project and customer names as untrusted input.
- Do not render raw user HTML.
- Use non-guessable sharing tokens.
- Apply reasonable API payload and rate limits before a public launch.
- Avoid collecting unnecessary personal data.
- V1 does not require customer accounts.

Because authentication is excluded, editable project URLs are demonstration-grade and not appropriate for sensitive customer designs. Authentication/ownership must be added before production multi-customer use.

---

## 20. Testing Strategy

### Unit tests

- Zod scene validation.
- Asset-dimension validation.
- Editor-store actions.
- Scene serialization and hydration.
- Share-token helpers.

### Component tests

- Catalogue rendering.
- Category filtering.
- Add-to-room action.
- Selected-object controls.
- Save dialog validation.
- Loading and error states.

### API tests

- Create project success/failure.
- Load project.
- Update project.
- Invalid scene rejection.
- Unknown asset rejection.
- Share creation.
- Invalid share token.
- Read-only response contract.

### End-to-end tests

At minimum:

1. Open editor and observe room/catalogue.
2. Add one product.
3. Save project.
4. Reopen project and verify product instance exists.
5. Generate share link.
6. Open shared project and verify editing actions are unavailable.

Precise pointer-based 3D transformation may require targeted integration tests and manual visual verification.

### Visual/manual verification

Every 3D or layout PR must include:

- Screenshot or short recording.
- Browser-console check.
- Confirmation that models sit on the floor and retain correct scale.
- Confirmation of camera and transform behaviour.
- Confirmation of no obvious mobile-layout regression.

---

## 21. Required Project Checks

The repository must provide scripts equivalent to:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

CI must run required checks on pull requests. A PR with failing required checks must not be merged.

---

## 22. Jared Development Workflow

1. One GitHub issue represents one coherent deliverable.
2. Every issue includes context, requirements, acceptance criteria, tests, and exclusions.
3. Jared creates a dedicated feature/fix branch.
4. Jared writes an implementation plan before material changes.
5. Jared implements only the assigned issue.
6. Jared runs relevant checks.
7. Jared removes unrelated or low-quality AI-generated changes.
8. Jared opens a draft PR with the plan and verification results.
9. CI failures are addressed with the smallest relevant fix.
10. Review comments are handled in the same PR/session.
11. The technical founder gives final approval and merge authority remains human-controlled.

### Agent constraints

Jared must not:

- Push directly to `main`.
- Merge its own PR.
- Implement excluded features.
- Add dependencies without explaining their necessity.
- Change architecture silently.
- Disable tests, lint rules, or type safety to obtain a passing build.
- Modify unrelated files.
- commit secrets, generated credentials, or production data.

---

## 23. Proposed Implementation Phases

Each phase should be split into reviewable GitHub issues.

### Phase 0 — Inputs and decisions

- Confirm temporary/release product name.
- Select demo brand identity.
- Obtain one room GLB.
- Obtain ten product GLBs, thumbnails, dimensions, SKUs, and prices.
- Verify rights to use demo assets.
- Define reference desktop and mobile devices.

### Phase 1 — Repository foundation

- Next.js + TypeScript + pnpm.
- Strict TypeScript.
- Tailwind CSS.
- shadcn/ui.
- Lucide React.
- Lint, typecheck, tests, build, CI.
- Environment validation.

### Phase 2 — Design system and shell

- Brand tokens.
- Header/navigation.
- Responsive editor shell.
- Loading/error primitives.
- Landing page.

### Phase 3 — Domain contracts and seed catalogue

- Asset schema/types.
- Scene schema/types.
- Seed ten products.
- Catalogue UI and filtering.

### Phase 4 — Static 3D room

- React Three Fiber canvas.
- Camera controls.
- Room model.
- Lighting and shadows.
- Progress and WebGL fallback.

### Phase 5 — Product placement

- Load GLB products.
- Add product instance.
- Selection.
- Floor-plane movement.
- Vertical-axis rotation.
- Delete instance.
- Reset scene.

### Phase 6 — Editor state and serialization

- Zustand store.
- Dirty state.
- Scene serialization.
- Scene hydration.
- Store unit tests.

### Phase 7 — Supabase persistence

- Database migrations.
- Storage configuration.
- Seed data.
- Project create/read/update routes.
- Save/reopen UI.
- API tests.

### Phase 8 — Sharing

- Secure token generation/storage.
- Share route.
- Read-only page.
- Share-link UI.
- End-to-end coverage.

### Phase 9 — Polish and validation

- Asset optimization.
- Performance review.
- Responsive review.
- Error-state review.
- Accessibility review.
- Business-demo script.
- Feedback sessions.

---

## 24. Definition of Done

An issue is done only when:

- Acceptance criteria are satisfied.
- Relevant tests are added or updated.
- Required checks pass.
- No unrelated changes are included.
- Error and loading states are handled.
- User-visible changes are visually verified.
- Documentation/contracts are updated when behaviour changes.
- The PR describes what changed, how it was tested, and any limitations.

The MVP is done only when:

- All primary flows work in the deployed environment.
- Ten demo products display at correct scale.
- Save/reopen preserves the scene.
- Sharing is read-only.
- Required checks pass.
- The demo has been visually verified on reference devices.
- Known limitations are documented.
- A repeatable demonstration script exists.

---

## 25. Open Decisions

These decisions must be resolved before their dependent issues begin:

1. Demo business identity and brand tokens.
2. Exact ten products and their legally usable assets.
3. Exact room GLB and licensing.
4. Reference devices/browser versions.
5. Whether V1 project links are intentionally public/unlisted or protected by a simple demo access gate.
6. Whether share links expire.
7. Whether prices are stored as whole AED values or minor units.
8. Whether camera position is persisted.
9. Whether undo/redo is included before pilot demonstrations.

Open decisions are not permission to expand scope. When an issue depends on one, the decision should be documented before implementation.

---

## 26. Future Architecture Direction

After V1 validation, possible additions include:

- Multi-tenant business accounts and branded portals.
- Catalogue-management dashboard.
- Customer authentication and project ownership.
- Quotation and lead workflows.
- WhatsApp sharing/integration.
- Product-photo-to-3D processing.
- Customer room upload and reconstruction.
- AR visualization.
- AI catalogue-constrained design assistant.
- Dedicated Node.js API.
- Queue and worker services for long-running jobs.
- Event, real-estate, interior-design, and hospitality configurations.

None of these future directions belong in V1 unless this specification is intentionally revised and approved.

---

## 27. One-Sentence Product Definition

> A branded browser-based furniture visualizer where customers place a business's real products inside a predefined 3D room, save the arrangement, and share a read-only design.

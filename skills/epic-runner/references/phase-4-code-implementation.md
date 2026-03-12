# Phase 4: Code Implementation

Your job is to implement the code changes described in the approved implementation plan.
Work through the plan layer by layer, following the project's clean architecture pattern.

## Prerequisites

Verify that:

- The implementation plan JSON exists (check the state file for `plan_file`).
- The user approved the plan (current phase should be `"implementing"`).

## Step 1: Load Context

1. Read the implementation plan JSON from the state file path.
2. Read the story document for acceptance criteria and design decisions.
3. Read the epic overview for resolved design decisions.
4. Read the project documentation:
   - `apps/lean-canvas-builder/docs/launch77/drizzle-api/README.md` — database patterns
   - `apps/lean-canvas-builder/docs/README.md` — app architecture
5. For each layer you're about to implement, read 1–2 adjacent existing files to match
   patterns. For example, before creating a new repository, read an existing repository
   in the same or similar module.

## Step 2: Create Feature Branch

**IMPORTANT**: Always branch from `develop`. Never branch from or merge into `main`.

```
git checkout develop && git pull origin develop
git checkout -b feature/epic-N-story-N-kebab-title
```

Update the state file with the branch name.

## Step 3: Implement Layer by Layer

Follow this order. Each layer should be completed and verified before moving to the next.
This order respects dependencies — data models must exist before repositories can use them,
services before resolvers, and so on.

### 1. Data Model Changes

If the implementation plan has `data_model_changes`:

- Create or modify Drizzle ORM model files under `src/modules/<module>/data/models/`
- Follow existing model patterns: `pgTable` definitions, type exports (`$inferSelect`, `$inferInsert`)
- Run `npm run db:generate` to generate migrations
- Run `npm run db:migrate` to apply migrations
- Verify migration success before continuing

### 2. Repository Layer

If the implementation plan has `repository_layer_changes`:

- Create or modify files under `src/modules/<module>/data/repositories/`
- Follow existing repository patterns:
  - Singleton export at bottom of file
  - Typed method signatures
  - Standard CRUD methods (findAll, findById, create, update, delete)
  - Drizzle query builder usage

### 3. Service Layer

If the implementation plan has `service_layer_changes`:

- Create or modify files under `src/modules/<module>/services/`
- Follow existing patterns:
  - Import repository singleton
  - Authorization checks where needed
  - Business logic validation
  - Singleton export

### 4. API Layer (GraphQL)

If the implementation plan has `api_layer_changes`:

- Create DTOs under `src/modules/<module>/api/dtos/` with TypeGraphQL decorators
- Create resolvers under `src/modules/<module>/api/resolvers/`
- Register new resolvers in `src/modules/graphql/schema-svc.ts` if needed
- Force-import DTOs for serverless bundling (check existing resolvers for the pattern)

### 5. Authorization Changes

If the implementation plan has `authorization_changes`:

- Create or modify policy files under `src/modules/<module>/policies/`
- Update auth types if needed
- Follow existing authorization patterns (e.g., `requireOrgAdminOrFGAdmin`)

### 6. UI Changes

If the implementation plan has `ui_changes`:

- Create or modify components following existing patterns in the project
- Use existing UI primitives from `@/modules/ui` before creating new ones
- Follow existing component organization:
  - Shared components: `src/components/shared-workspace/`
  - Workspace-specific: `src/components/<workspace>-workspace/`
  - Module-specific: `src/modules/<module>/components/`
- Create or modify route pages under `src/app/`
- Follow Next.js App Router conventions (page.tsx, layout.tsx, loading.tsx)

### 7. Routing & Navigation Changes

If the implementation plan has `routing_changes` or `navigation_changes`:

- Create new route directories and page files
- Update navigation components (sidebar items, header links)
- Create stub/placeholder pages for "Coming soon" features if needed

### 8. State Management

If the implementation plan has `state_management_changes`:

- Add GraphQL query/mutation strings to appropriate `graphql.ts` files
- Follow existing patterns for client-side data fetching
- Use existing hooks and context patterns — avoid introducing new state management tools

## Step 4: Verify Each Layer

After completing each layer (not just at the end):

- Run `npm run lint` to check for lint errors
- Fix any errors before moving to the next layer

After completing all layers:

- Run `npm run build` to verify the build succeeds (this also catches type errors)

## Step 5: Implementation Rules

These rules apply throughout:

- **Match existing code style.** Check adjacent files for formatting, naming, and import
  patterns before writing new code.
- **Use existing components before creating new ones.** Search the codebase first.
- **Follow the story's design decisions.** They are hard constraints, not suggestions.
- **Respect "Areas This Story Must Not Change"** — if the story document or implementation
  plan lists files or areas to avoid, do not touch them.
- **No gold-plating.** Implement what the plan says. Do not add features, refactor adjacent
  code, or "improve" things outside the plan's scope.

## Step 6: Progress Report

After completing implementation, present:

1. **Files created** — list with brief description of each
2. **Files modified** — list with summary of changes
3. **Decisions made** — any judgment calls during implementation
4. **Build status** — typecheck and lint results
5. **Migration status** — if any database changes were made

## Step 7: Approval Gate

Ask: "Implementation is complete. Ready to move to testing, or do you want to review
anything first?"

If the user wants to review specific files, show them. If they request changes, apply them
and re-verify.

## Step 8: State File Update

After the user confirms:

1. Set `current_phase` to `"testing"`.
2. Update `updated_at`.
3. Tell the user: "Ready for testing. Run `/epic-runner test` to start."

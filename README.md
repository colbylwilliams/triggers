# triggers

A test repository for workflow triggers — specifically testing the pattern where a CI workflow fails and a Copilot agent is automatically triggered to analyze and fix the failure.

## Workflows

| Workflow | File | Description |
|----------|------|-------------|
| **Check Code Comments** | `.github/workflows/check-comments.yaml` | Scans source files for `// BROKEN`, `// FIXME`, `// HACK` comments and fails if any are found |
| **Build** | `.github/workflows/build.yaml` | Runs `npm ci` and `tsc` in the `app/` directory; fails on TypeScript compilation errors |
| **Create Test PR** | `.github/workflows/create-test-pr.yaml` | Creates a PR that introduces a chosen failure type (forbidden comment, TypeScript error, or both) to test CI triggers |

## Agents

| Agent | File | Trigger |
|-------|------|---------|
| **CI Repair** | `.github/agents/fix-ci.md` | `workflow_run: failed` on Check Code Comments and Build workflows |
| **Triager** | `.github/agents/triager.md` | `issues: opened` |
| **Joker** | `.github/agents/joker.md` | Daily interval |

## How to trigger a workflow failure

### Method 1: Create Test PR workflow (easiest)

Run the **Create Test PR** workflow from the Actions tab. Choose a failure type (`forbidden-comment`, `typescript-error`, or `both`) and it will automatically create a PR that triggers the corresponding CI failure.

### Method 2: Forbidden comment

Add a forbidden comment to any `.ts` or `.js` file and push:

```typescript
// HACK this needs to be refactored
```

The **Check Code Comments** workflow will detect it and fail, triggering the CI Repair agent.

### Method 3: TypeScript compilation error

Introduce a type error in `app/src/index.ts` and push:

```typescript
const port: number = "not a number"; // type error
```

The **Build** workflow will fail on `tsc`, triggering the CI Repair agent.

### Method 4: Manual dispatch

Both CI workflows support `workflow_dispatch` — trigger them manually from the Actions tab.

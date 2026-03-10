# triggers

A test repository for workflow failed triggers — specifically testing the pattern where a CI workflow fails and a Copilot agent is automatically triggered to analyze and fix the failure.

## Workflows

| Workflow | File | Description |
|----------|------|-------------|
| **Check Code Comments** | `.github/workflows/check-comments.yaml` | Scans source files for `// BROKEN`, `// FIXME`, `// HACK` comments and fails if any are found |
| **Build** | `.github/workflows/build.yaml` | Runs `npm ci` and `tsc` in the `app/` directory; fails on TypeScript compilation errors |
| **Create Test PR** | `.github/workflows/create-test-pr.yaml` | Creates a branch and PR with an introduced failure to test CI triggers; supports `forbidden-comment`, `typescript-error`, or `both` |

## Agents

| Agent | File | Trigger |
|-------|------|---------|
| **fix-ci** | `.github/agents/fix-ci.md` | `workflow_run: failed` on Check Code Comments and Build workflows |
| **Triager** | `.github/agents/triager.md` | `issues: opened` |
| **Joker** | `.github/agents/joker.md` | `interval: daily` |

## How to trigger a workflow failure

### Method 1: Create Test PR workflow (easiest)

Use the **Create Test PR** workflow dispatch from the Actions tab. Select a failure type (`forbidden-comment`, `typescript-error`, or `both`) and the workflow will automatically create a branch and open a PR with the chosen failure introduced.

### Method 2: Forbidden comment (manual)

Add a forbidden comment to any `.ts` or `.js` file and push:

```typescript
// HACK this needs to be refactored
```

The **Check Code Comments** workflow will detect it and fail, triggering the fix-ci agent.

### Method 3: TypeScript compilation error (manual)

Introduce a type error in `app/src/index.ts` and push:

```typescript
const port: number = "not a number"; // type error
```

The **Build** workflow will fail on `tsc`, triggering the fix-ci agent.

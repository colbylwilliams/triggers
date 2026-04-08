# triggers

A test repository for GitHub Copilot agent workflow triggers — specifically testing the pattern where a CI workflow fails and a Copilot coding agent is automatically triggered to analyze and fix the failure.

## How it works

```
Push / PR ──► CI workflow runs ──► Workflow fails ──► Copilot coding agent triggered
                                                          │
                                                          ▼
                                                     Agent analyzes logs,
                                                     fixes the code, and
                                                     pushes a commit
```

1. A push or pull request runs the CI workflows (**Build** and **Check Code Comments**).
2. If a workflow fails, the `workflow_run` event fires.
3. The **CI Repair** coding agent picks up the failure, reads the logs, and attempts an automated fix.

## Project structure

```
triggers/
├── app/                          # Minimal Express + TypeScript application
│   ├── src/
│   │   └── index.ts              # Express server with / and /health endpoints
│   ├── package.json
│   └── tsconfig.json
├── .github/
│   ├── workflows/
│   │   ├── build.yaml            # TypeScript build CI
│   │   ├── check-comments.yaml   # Forbidden-comment linter CI
│   │   └── create-test-pr.yaml   # Helper: creates a PR with intentional failures
│   └── agents/
│       ├── fix-ci.md             # CI Repair agent instructions
│       ├── triager.md            # Issue triager agent instructions
│       └── joker.md              # Joker agent instructions
└── README.md
```

## Workflows

| Workflow | File | Trigger | Description |
|----------|------|---------|-------------|
| **Build** | [`build.yaml`](.github/workflows/build.yaml) | `push`, `pull_request`, `workflow_dispatch` | Runs `npm ci` and `tsc` in the `app/` directory; fails on TypeScript compilation errors |
| **Check Code Comments** | [`check-comments.yaml`](.github/workflows/check-comments.yaml) | `push`, `pull_request`, `workflow_dispatch` | Scans `.ts`, `.js`, `.tsx`, and `.jsx` files for `// BROKEN`, `// FIXME`, and `// HACK` comments and fails if any are found |
| **Create Test PR** | [`create-test-pr.yaml`](.github/workflows/create-test-pr.yaml) | `workflow_dispatch` | Creates a PR that intentionally introduces a failure (`forbidden-comment`, `typescript-error`, or `both`) to test the trigger chain end-to-end |

## Agents

| Agent | File | Trigger | Description |
|-------|------|---------|-------------|
| **CI Repair** | `.github/agents/fix-ci.md` | `workflow_run: failed` on Build and Check Code Comments | Analyzes failed CI logs and pushes an automated fix |
| **Triager** | `.github/agents/triager.md` | `issues: opened` | Triages newly opened issues |
| **Joker** | `.github/agents/joker.md` | `schedule` (daily) | Opens a lighthearted joke issue on a daily schedule |

## The app

The `app/` directory contains a minimal [Express](https://expressjs.com/) server written in TypeScript. It exists primarily as a build target for the CI workflows.

### Endpoints

| Method | Path | Response |
|--------|------|----------|
| `GET` | `/` | `{ "message": "Hello from triggers-app!" }` |
| `GET` | `/health` | `{ "status": "ok" }` |

### Prerequisites

- [Node.js](https://nodejs.org/) 20+

### Running locally

```bash
cd app
npm install
npm run build   # compile TypeScript
npm start       # start the server (default port 3000)
```

During development you can use `npm run dev` to run with `ts-node` without a separate build step.

## How to trigger a workflow failure

### Create Test PR workflow (easiest)

Use the **Create Test PR** workflow from the **Actions** tab. Choose a failure type (`forbidden-comment`, `typescript-error`, or `both`) and the workflow will create a branch and open a PR with the intentional failure automatically.

### Forbidden comment

Add a forbidden comment to any `.ts` or `.js` file and push:

```typescript
// HACK this needs to be refactored
```

The **Check Code Comments** workflow will detect it and fail, triggering the CI Repair agent.

### TypeScript compilation error

Introduce a type error in `app/src/index.ts` and push:

```typescript
const port: number = "not a number"; // type error
```

The **Build** workflow will fail on `tsc`, triggering the CI Repair agent.

### Manual dispatch

Both CI workflows support `workflow_dispatch` — trigger them manually from the **Actions** tab.

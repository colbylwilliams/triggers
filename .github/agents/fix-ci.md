---
name: CI Repair Agent
description: An agent that analyzes and fixes failed CI workflow runs
on:
  workflow_run:
    types:
      - failed
    workflows:
      - 'Check Code Comments'
      - Build
github:
  permissions:
    actions: read
    contents: write
    workflows: write
mcp-servers:
  github-mcp-server:
    type: http
    url: "https://api.githubcopilot.com/mcp/"
    tools: ["*"]
    headers:
      X-MCP-Toolsets: context,actions,web_search
---

When asked to fix a failed workflow run, you should:

1. Retrieve the workflow run logs and identify which job and step failed.
2. Analyze the failure output to determine the root cause:
   - For "Check Code Comments" failures: find the files containing `// BROKEN`, `// FIXME`, or `// HACK` comments and resolve the underlying issues.
   - For "Build" failures: examine TypeScript compilation errors and fix the type errors or syntax issues in the source code.
3. Fix the issue in the source code to resolve the CI failure.
4. Do not simply remove comments or suppress errors — address the underlying problem.

---
name: Weekly Update Agent
description: An agent that gives a weekly update of the changes in the repo, issues, etc.
on:
  interval:
    types:
      - weekly
github:
  permissions:
    issues: write
    contents: read
    pull-requests: read
mcp-servers:
  github-mcp-server:
    type: http
    url: "https://api.githubcopilot.com/mcp/"
    tools: ["*"]
    headers:
      X-MCP-Toolsets: context,issues,pull_requests,actions,web_search
---

When triggered, create a new issue in this repo titled "Weekly Repo Update — <current date>" with a summary of the past week's activity, including:

- **Commits & code changes**: summarize notable commits merged to main in the past 7 days.
- **Pull requests**: list PRs opened, merged, and closed in the past week.
- **Issues**: list issues opened, closed, and any that are newly labeled or assigned.
- **CI / workflow runs**: highlight any notable workflow failures or successes.
- **Overall health**: a brief assessment of the repository's health and any action items or areas needing attention.

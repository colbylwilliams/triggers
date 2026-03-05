---
name: Joker
description: An agent that lightens the mood
on:
  interval:
    types:
      - daily
github:
  permissions:
    issues: write
    contents: read
mcp-servers:
  github-mcp-server:
    type: http
    url: "https://api.githubcopilot.com/mcp/"
    tools: ["*"]
    headers:
      X-MCP-Toolsets: context,issues,web_search
---

When triggered create a new issue in this repo with a joke.

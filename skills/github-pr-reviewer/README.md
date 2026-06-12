# GitHub PR Reviewer

Create an automation that reviews GitHub pull requests on open or update.

## Triggers

This skill is activated by keywords:

- `review pull requests`
- `PR review automation`
- `auto-review PRs`

## Features

- **Inspects PR diff, changed files, and test coverage**
- **Posts review with correctness risks, security issues, missing tests**
- **Supports event-based (webhook) or cron-based (polling) triggers**
- **Configurable review tone (thorough, concise, friendly)**
- **Auto-post or draft mode for human approval**
- **Uses reusable helper scripts for packaging and automation creation**
- **Keeps generated build files in the system temporary directory instead of the repository**

## Prerequisites

GitHub MCP installed in Settings → MCP

## Quick Start

Ask OpenHands:

> "Set up a PR review automation for my myorg/backend repo that posts
> concise reviews when PRs are opened"

## Helper Scripts

- `scripts/main.py` — automation script template to customize before upload
- `scripts/package_upload.py` — packages and uploads a prepared build directory
- `scripts/create_automation.py` — creates the cron automation from the uploaded tarball

## See Also

- [SKILL.md](SKILL.md) — Full setup workflow reference

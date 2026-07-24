# GitHub Actions Contributor & Community Workflows

This document provides a comprehensive guide to the automated GitHub Actions workflows configured in **YuvaHub**. These workflows automate contributor onboarding, engagement, issue assignment notifications, PR merge celebrations, label management, and workflow customization.

---

## 📐 Workflow Architecture

| Workflow File | Triggering Events | Purpose & Deliverable |
|---|---|---|
| [`issue-automation.yml`](../.github/workflows/issue-automation.yml) | `issues.opened`<br>`issues.assigned` | • Differentiates 1st-time vs returning issue creators.<br>• Sends welcome message with repo resources & SLAs.<br>• Adds `needs review` label.<br>• Congratulates & instructs assigned contributors. |
| [`pr-automation.yml`](../.github/workflows/pr-automation.yml) | `pull_request_target.opened`<br>`pull_request_target.closed` | • Differentiates 1st-time vs returning PR contributors.<br>• Sends PR welcome message & checklist reminder.<br>• Adds `needs review` label.<br>• Celebrates merged pull requests. |
| [`label-management.yml`](../.github/workflows/label-management.yml) | `pull_request_review.submitted` | • Removes `needs review` label when maintainer review is submitted. |
| [`pr-body-checker.yml`](../.github/workflows/pr-body-checker.yml) | `pull_request.opened`<br>`pull_request.edited`<br>`pull_request.synchronize` | • Validates PR description structure against required sections and issue linking keywords. |
| [`pr-title.yml`](../.github/workflows/pr-title.yml) | `pull_request.opened`<br>`pull_request.edited`<br>`pull_request.synchronize` | • Enforces conventional commit standards on PR titles. |
| [`ci.yml`](../.github/workflows/ci.yml) | `push`, `pull_request` | • Builds, lints, and runs tests across Node.js matrix. |

---

## ⚙️ Maintainer Configuration (`.github/community-config.json`)

All automated message links, response expectations, label names, and working ethics are stored in [`.github/community-config.json`](../.github/community-config.json). Maintainers can easily update these settings without touching workflow YAML files.

### Configuration Schema

```json
{
  "repository": {
    "name": "YuvaHub",
    "owner": "uditt490-pixel",
    "url": "https://github.com/uditt490-pixel/YuvaHub"
  },
  "links": {
    "contributing": "https://github.com/uditt490-pixel/YuvaHub/blob/main/CONTRIBUTING.md",
    "code_of_conduct": "https://github.com/uditt490-pixel/YuvaHub/blob/main/CODE_OF_CONDUCT.md",
    "documentation": "https://github.com/uditt490-pixel/YuvaHub/tree/main/docs",
    "issue_templates": "https://github.com/uditt490-pixel/YuvaHub/issues/new/choose",
    "pr_template": "https://github.com/uditt490-pixel/YuvaHub/blob/main/.github/PULL_REQUEST_TEMPLATE.md",
    "discussions": "https://github.com/uditt490-pixel/YuvaHub/discussions",
    "discord": "https://discord.gg/yuvahub",
    "slack": "https://yuvahub.slack.com",
    "support": "https://github.com/uditt490-pixel/YuvaHub/issues"
  },
  "expectations": {
    "response_time": "24 to 48 hours"
  },
  "labels": {
    "needs_review": "needs review"
  },
  "working_ethics": [
    "Work strictly on assigned issues to avoid duplicate efforts.",
    "Keep maintainers regularly updated on your progress.",
    "Ask questions in the issue thread whenever you hit a blocker.",
    "Avoid force-pushing commits without clear reason or maintainer alignment.",
    "Submit clean, focused Pull Requests that address a single concern.",
    "Follow repository coding standards and format guidelines.",
    "Be respectful, patient, and collaborative with fellow contributors."
  ],
  "pr_checklist": [
    "Verify changes pass all local builds & tests.",
    "Follow repository coding conventions and formatting standards.",
    "Link the corresponding issue using keywords (e.g. `Closes #123`).",
    "Provide a clear summary and visual screenshots/GIFs for UI modifications.",
    "Ensure PR remains clean and free of extraneous file changes."
  ]
}
```

---

## 🔍 Key Feature Details

### 1. First-Time vs. Returning Contributor Detection
Workflows search GitHub repository history using the Octokit REST API:
- For Issues: `q: repo:owner/repo type:issue author:username`
- For PRs: `q: repo:owner/repo type:pr author:username`

If the search count equals `1` (the currently opened event), the system sends an extended **First-Time Welcome** message containing full onboarding links, community guides, and star/fork calls to action. For count `> 1`, a concise **Welcome Back** message is delivered.

### 2. Issue Assignment Ethics & Resources
When an issue is assigned to a contributor (`issues: assigned`), the workflow sends an automated comment congratulating `@assignee`, detailing recommended working ethics, and providing quick links to setup guides, documentation, and support channels.

### 3. PR Merge Celebrations
When a PR is closed with `merged == true`, the `pr-automation.yml` workflow posts a celebration comment congratulating `@author`, thanking them for their code contribution, and inviting them to explore more issues, join discussions, or star the project.

### 4. Label Automation Lifecycle
- **Newly Opened Issues & PRs:** Automatically labeled with `needs review` (the label is automatically created with description & color if missing).
- **PR Review Submitted:** When a maintainer submits a PR review, `label-management.yml` automatically removes `needs review`.

---

## 🔒 Security & Permission Setup

Workflows operate under standard `GITHUB_TOKEN` permissions:

```yaml
permissions:
  issues: write
  pull-requests: write
  contents: read
```

`pull_request_target` is utilized for PR onboarding workflows so that PRs originating from external forks possess sufficient write permissions to post comments and add labels safely without exposing repository secrets.

---

## 🛠️ Maintenance & Troubleshooting

1. **Updating Links or SLA:** Edit `.github/community-config.json` directly. No workflow modifications required.
2. **Changing Review Label:** Modify `"needs_review"` in `.github/community-config.json`.
3. **Checking Workflow Logs:** Go to **Actions** tab in GitHub to inspect execution logs if any step fails.

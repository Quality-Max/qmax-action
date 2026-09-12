> **Archived: migrate to [QualityMax GitHub Action](https://github.com/Quality-Max/qualitymax-github-action).**
> The examples below describe the retired integration. Start new workflows from the maintained Action's README and review the migration notes here before changing an existing workflow.

## Migration to the maintained Action

Changing only the `uses` repository is not sufficient. Compare the two action manifests and update your workflow:

| Archived input/output | Maintained integration |
| --- | --- |
| `api-token` input | `api-key` input; store the credential in GitHub Actions secrets |
| `comment` input | `post-pr-comment` input |
| `script-ids` input | Review `test-suite` and `test-ids`; do not assume script IDs and test IDs are interchangeable |
| `project-id` input | Still supported; the maintained Action also documents `project-name` and repository detection |
| `base-url`, `browser`, `fail-on-test-failure` | Same input names; check current defaults and behavior |
| `qmax-version` input | No corresponding input in the maintained Action |
| `total`, `passed`, `failed`, `duration`, `summary` outputs | `total-tests`, `passed-tests`, `failed-tests`, `duration-seconds`, `summary-markdown` |

Recreate test selection, permissions, and result handling from the [maintained configuration reference](https://github.com/Quality-Max/qualitymax-github-action#inputs). Validate a completed run on a test project before making the new check required. See [QualityMax documentation](https://docs.qualitymax.io/) and the [gate guide](https://docs.qualitymax.io/code-review-gates/) for the separate GitHub App workflow.

---

# QualityMax Test Runner

Run [QualityMax](https://qualitymax.io) tests in your CI/CD pipeline. AI-powered test automation with quality gates.

## Quick Start

```yaml
name: Tests
on: [pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: Quality-Max/qmax-action@v1
        with:
          api-token: ${{ secrets.QMAX_TOKEN }}
          project-id: '42'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `api-token` | Yes | — | QualityMax API token |
| `project-id` | Yes | — | Project ID |
| `script-ids` | No | all | Comma-separated script IDs |
| `base-url` | No | — | Base URL override (e.g. preview deploy URL) |
| `browser` | No | `chromium` | Browser: chromium, firefox, webkit |
| `comment` | No | `true` | Post results as PR comment |
| `fail-on-test-failure` | No | `true` | Fail action on test failures |
| `qmax-version` | No | `latest` | qmax CLI version |

## Outputs

| Output | Description |
|--------|-------------|
| `status` | `passed` or `failed` |
| `total` | Total test count |
| `passed` | Passed count |
| `failed` | Failed count |
| `duration` | Duration in seconds |
| `summary` | Markdown summary |

## Examples

### Run specific tests
```yaml
- uses: Quality-Max/qmax-action@v1
  with:
    api-token: ${{ secrets.QMAX_TOKEN }}
    project-id: '42'
    script-ids: '101,102,103'
```

### Test preview deploys (Vercel/Netlify)
```yaml
- uses: Quality-Max/qmax-action@v1
  with:
    api-token: ${{ secrets.QMAX_TOKEN }}
    project-id: '42'
    base-url: ${{ steps.deploy.outputs.url }}
```

### Non-blocking (don't fail on test failures)
```yaml
- uses: Quality-Max/qmax-action@v1
  with:
    api-token: ${{ secrets.QMAX_TOKEN }}
    project-id: '42'
    fail-on-test-failure: 'false'
```

## Setup

1. Get your API token from [QualityMax Settings](https://app.qualitymax.io/#/settings)
2. Add it as a repository secret: `QMAX_TOKEN`
3. Add the action to your workflow

## How it works

1. Installs the [`qmax` CLI](https://github.com/Quality-Max/qmax-local-agent)
2. Authenticates with your API token (headless, no browser needed)
3. Runs your test suite on QualityMax infrastructure
4. Posts results as a PR comment
5. Fails the workflow if tests fail (quality gate)

---

Built by [QualityMax](https://qualitymax.io) — AI-powered test automation

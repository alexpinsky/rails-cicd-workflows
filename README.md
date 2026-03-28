# rails-cicd-workflows

Shared GitHub Actions reusable workflow for Rails apps deployed via Coolify.

All apps reference this instead of copy-pasting CI config. Fix CI for every app at once by editing here.

## Usage

In your app's `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  ci:
    uses: alexpinsky/rails-cicd-workflows/.github/workflows/rails-ci.yml@main
    secrets:
      COOLIFY_WEBHOOK_URL: ${{ secrets.COOLIFY_WEBHOOK_URL }}
      COOLIFY_API_TOKEN: ${{ secrets.COOLIFY_API_TOKEN }}
```

## Inputs (optional)

| Input | Default | When to use |
|---|---|---|
| `skip-system-tests` | `false` | API-only apps with no browser UI |
| `skip-scan-js` | `false` | Apps not using importmap |
| `extra-apt-packages` | `""` | Apps needing extra packages (e.g. `libvips-dev`) |

Example with options:

```yaml
jobs:
  ci:
    uses: alexpinsky/rails-cicd-workflows/.github/workflows/rails-ci.yml@main
    with:
      skip-system-tests: true
    secrets:
      COOLIFY_WEBHOOK_URL: ${{ secrets.COOLIFY_WEBHOOK_URL }}
      COOLIFY_API_TOKEN: ${{ secrets.COOLIFY_API_TOKEN }}
```

## Required GitHub secrets (per app repo)

- `COOLIFY_WEBHOOK_URL` — set automatically by `rails-bootstrap new-app`
- `COOLIFY_API_TOKEN` — set automatically by `rails-bootstrap new-app`

## Jobs

| Job | What it does |
|---|---|
| `scan_ruby` | Brakeman (security) + bundler-audit (gem vulnerabilities) |
| `scan_js` | importmap audit (JS dependency vulnerabilities) |
| `lint` | RuboCop with caching |
| `test` | Minitest with PostgreSQL service |
| `system-test` | Capybara + headless Chrome, uploads screenshots on failure |
| `deploy` | Triggers Coolify webhook (main branch pushes only, after all checks pass) |

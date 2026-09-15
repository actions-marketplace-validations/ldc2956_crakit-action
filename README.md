# crakit-action

**SBOM + known vulnerabilities + CISA KEV check in your CI, with the EU Cyber Resilience Act clock.**
Wraps [`crakit-cli`](https://www.npmjs.com/package/crakit-cli) (MIT, no account, no telemetry). One step, fails the job when a dependency is on the Known Exploited Vulnerabilities list, which is exactly the event Article 14 of the CRA makes you report within 24 hours from 11 September 2026.

```yaml
- uses: ldc2956/crakit-action@v1
```

That is the whole integration. It writes `sbom.cdx.json` (CycloneDX 1.5) and `crakit-report.json`, uploads both as artifacts, and prints a summary on the job page.

## Options

```yaml
- uses: ldc2956/crakit-action@v1
  with:
    fail-on-kev: 'true'        # default: fail when a KEV-listed vulnerability is found
    fail-on: high              # optional: also fail at or above a severity (low|moderate|high|critical)
    lang: en                   # or fr
    working-directory: .       # one directory level; run once per package in a monorepo
    no-dev: 'false'            # exclude dev dependencies from the SBOM
    product-name: my-app       # SBOM metadata
    product-version: 1.4.0
    cli-version: latest        # pin a crakit-cli version for reproducible builds
    upload-artifacts: 'true'
```

Outputs: `kev-count`, `vuln-count`, `sbom`, `exit-code` (0 ok, 2 KEV hit, 3 severity hit).

Supported inputs: `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `requirements.txt`, `poetry.lock`, `uv.lock`, `Pipfile.lock`, `go.sum`, `Cargo.lock`, `composer.lock`, `Gemfile.lock`, `pom.xml`, `packages.lock.json`, `pubspec.lock`, `Package.resolved`, and existing CycloneDX / SPDX files.

## A weekly run, not only on push

The KEV list changes every day. A dependency that was clean on Monday can be "actively exploited" on Thursday without a single commit on your side. Add a schedule so the clock never starts without you knowing:

```yaml
name: crakit
on:
  push:
  pull_request:
  schedule:
    - cron: '17 6 * * *'   # every morning
jobs:
  cra:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ldc2956/crakit-action@v1
```

## Badges

Two badges you can put in your README. Both say something true.

**Workflow status**, from GitHub itself: green means the last scan found nothing on the KEV list.

```markdown
[![CRA Kit scan](https://github.com/OWNER/REPO/actions/workflows/crakit.yml/badge.svg)](https://github.com/OWNER/REPO/actions/workflows/crakit.yml)
```

**Static badge**, to say the project checks its dependencies against KEV:

```markdown
[![KEV checked with CRA Kit](https://img.shields.io/badge/KEV-checked_with_CRA_Kit-1b3fa0)](https://crakit.eu)
```

What the badges do not say: that your product is "CRA compliant". Nothing can say that for you. The scan covers one of the vulnerability-handling requirements (Annex I, Part II). The rest is documents: a coordinated disclosure policy, a `security.txt`, the 24 h / 72 h reporting procedure, the technical documentation. [crakit.eu](https://crakit.eu) has a free scope test and generates those documents from one form.

## What leaves your runner

Package names and versions, sent to `api.osv.dev`. Nothing else. The KEV catalogue is fetched from a static mirror. No account, no key, no telemetry.

## Versioning

`@v1` follows the latest 1.x tag. Pin `@v1.0.0` if you prefer, and `cli-version` to pin the CLI too.

MIT. Built by Louann Duclos. Not legal advice.

# Security Review: teslatlas-viewer

## Scope

Offline static Standard audit of deferred Teslatlas Viewer at d912d276 plus pre-existing dirty AGENTS.md.

- Scan mode: repository
- Target kind: git_worktree
- Target ID: target_sha256_6882ba992da943d7910f8b7a775a449383237afa8ea16364406a54e53fee7bd9
- Revision: d912d2760ba08db775e95c5f35804f13dd6ae9c5
- Snapshot digest: codex-security-snapshot/v1:sha256:0d7e4696f53c27e2021ad128e5c08e2fa0d07851b55cd3b0fcf7cecb1d79e3d7
- Inventory strategy: repository
- Included paths: .
- Excluded paths: none
- Artifacts reviewed: AGENTS.md, .github/SECURITY.md, README.md, package.json, src/main.tsx, src/data/sdk-data-source.ts, src/data/create-data-source.ts, src/app/App.tsx, src/app/use-viewer.ts, src/components/connection-panel.tsx, selected views, bin/teslatlas-viewer.mjs, Dockerfile, compose.yaml, vite.config.ts, playwright.hub.config.ts, selected scripts/tests, docs/architecture/overview.md, docs/legal/privacy.md

Limitations and exclusions:
- No build, tests, server, pairing, browser, live Hub, network, credentials or runtime evidence.
- Full test/fixture/output/archived-doc corpus not exhaustive.
- Existing dirty AGENTS.md untouched.

### Scan Summary

| Field | Value |
| --- | --- |
| Scan outcome | completed |
| Reportable findings | 0 |
| Severity mix | none |
| Confidence mix | none |
| Coverage | partial |
| Validation mode | static |

Canonical artifacts: `scan-manifest.json`, `findings.json`, and `coverage.json`. This report is a deterministic projection of those files.

## Threat Model

Deferred browser reference Viewer uses a public SDK adapter to display Hub data and pairing/session state; it is not a protocol authority or v7 product.

### Assets

- Pairing invitation and in-memory Hub bearer
- Private vehicle/session/paired-device data rendered in browser
- Browser origin and DOM integrity
- Container/fixture/evidence outputs

### Trust Boundaries

- User-supplied Hub endpoint and invitation into SDK adapter
- Remote Hub/SDK data and errors into React views and DOM
- In-memory credential state across pairing, logout and page lifecycle
- Local development/container server and browser capture scripts

### Attacker Capabilities

- Malicious or compromised configured Hub may send crafted strings and errors
- Web attacker may attempt cross-origin reads or inject through rendered data
- Local actor may access development server or generated evidence when run
- User may paste untrusted endpoint/invitation

### Security Objectives

- Keep pairing/bearer out of URLs, storage, logs, screenshots and fixtures
- Render untrusted Hub data without script injection
- Maintain SDK as sole network boundary and ensure local clear is not confused with revocation
- Avoid unintended public server exposure

### Assumptions

- Viewer is deferred and must not be built/tested/served/paired; source-only audit explicitly requested.
- No live Hub, browser, network, credentials or runtime evidence.
- AGENTS.md has pre-existing user changes, preserved untouched.

## Findings

### No findings

No reportable findings survived the canonical discovery, validation, and reportability gates.

## Reviewed Surfaces

| Surface | Risk Area | Outcome | Notes |
| --- | --- | --- | --- |
| Docs/fixtures/tests | Security-contract and secret hygiene | Needs follow-up | Relevant policy and tests targeted, no runtime execution or exhaustive fixture/archived-doc review. |
| Connection and pairing state | Secret lifecycle | No issue found | Root HTTPS, Hub UUID/invitation origin checks; bearer held in memory only and cleared with generation guards. Browser claim lacks pin-capable transport and appears fail-closed/nonfunctional. |
| SDK adapter boundary | Origin/request/response | No issue found | SDK adapter is the only live network boundary; no view-owned fetch or credential persistence found. |
| Views and DOM rendering | XSS/private-data display | No issue found | Hub labels and errors render as React text; no raw HTML or dynamic Hub-derived href/src sink found. |
| Server/container and evidence scripts | Exposure and artifact data | No issue found | Static server uses containment/traversal checks, defaults loopback; Compose maps loopback. Public HTTP requires explicit operator choice. |

## Open Questions And Follow Up

- Browser claim flow appears fail-closed without claimTransport; functional implementation is deferred and not a security bypass.
- If Viewer is ever publicly served, deployment must enforce HTTPS for the page itself; default/Compose bind loopback.
- Viewer deferred; no authorized runtime/browser/lab checks. Remaining tests, fixtures, archived docs and output assets not exhaustively reviewed.
  - Follow-up prompt: Review deferred unit viewer-runtime-and-residual and close its stated proof gap. Paths: e2e, output, docs/development, src.

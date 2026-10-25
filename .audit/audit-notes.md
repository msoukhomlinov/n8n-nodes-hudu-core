# Known dev-tree advisory residuals

`npm audit` on this package reports residual advisories that are **deliberately
accepted** (documented rejects, 2.5.1). They exist only in the development
toolchain and are not shippable or fixable from this repo.

## Why this is acceptable

- Every flagged package lives under `devDependencies` (build / lint / release tooling).
- The package has **zero runtime dependencies**: no `dependencies` block, the tarball
  ships `dist` only (`files: ["dist"]`).
- The only peer dependency is `n8n-workflow: "*"`, provided by the n8n platform at
  runtime. None of the flagged code ships or executes in an n8n instance.
- npm `overrides` are **forbidden** for community node packages by the scanner rule
  `@n8n/community-nodes/no-overrides-field` — an `overrides` block makes both
  `node docs/dev-tools/scancheck.mjs` and `npm run lint` fail (verified in scratch
  runs during the 2.5.1 audit-hygiene triage). So the in-major fixes below cannot be
  delivered through this repo at all.

## Reject classes (one line each)

| class | reason |
|---|---|
| `n8n-workflow` | platform peer (n8n core); vulnerable range `>=1.76.0` covers every published version; npm's "fix" is a downgrade to 1.75.1 — waits for an n8n upstream release |
| `zod` v3 line | advisory range `<4.6.6` covers all of v3; the fix is zod 4, a breaking major across the whole @n8n platform toolchain — awaits n8n upstream |
| `@n8n/*` platform chain | config, api-types, permissions, expression-runtime, decorators, backend-common, backend-network, ai-utilities, ai-node-sdk, utils, eslint-plugin-community-nodes, node-cli: flags are derived via the zod/axios/@n8n/utils leaves; no published version escapes |
| `@n8n/scan-community-package` | advisory range `0.2.0-0.9.5 \|\| >=0.10.0` (via `tmp` <0.2.6 + `eslint-plugin-community-nodes`) covers every modern version incl. 0.40.0; npm's "fix" is a downgrade to 0.9.8 — no published version escapes; kept at `^0.40.0` for gate currency (the 0.40.0 generation enforces `no-overrides-field`) — joins the platform-chain reject class |
| `undici` | all 6.x/7.x lines fully vulnerable; fix only in 8.x — cross-major inside `release-it`'s tree (its only home here) |
| `proxy-agent` / `pac-proxy-agent` / `get-uri` | vulnerable ranges `>=6.0.0` — no fixed version published at all (dead-end, via `release-it`) |
| `basic-ftp` / `stream-json` / `uuid` | fixes only in new majors (6.2.x / 3.5.1+ / 11.1.1+); installed 5.3.1 / 1.9.1 / 10.0.0 |
| `brace-expansion` | fixed 5.0.12 exists within-major, but the only delivery mechanism (npm override) is forbidden by the gate; a global pin also breaks the scanner's nested `minimatch@3.1.5` (verified) |
| `release-it` chain | even 21.1.1 still pulls the undici / proxy-agent / basic-ftp chain; no published `release-it` escapes it |

Residual after the 2.5.1 dev-toolchain pins: see `npm-audit-postfix.json` in the run
dir (`/tmp/hudu-core-2.5.1-audit`) — expected floor is the table above plus the
`handlebars` critical carried by `@n8n/node-cli` 0.39.3 (fixed only in an n8n platform
release; the exact pin keeps the build gate deterministic on fresh installs).

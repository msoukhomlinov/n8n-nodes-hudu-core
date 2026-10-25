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
| `basic-ftp` / `stream-json` / `uuid` / `js-yaml` | fixes only in new majors (6.2.x / 3.5.1+ / 11.1.1+ / v5); installed 5.3.1 / 1.9.1 / 10.0.0 / 4.3.0 |
| `follow-redirects` | 2 GHSAs (`>=1.16.0 <1.16.1`, installed 1.16.0) carried by the axios subtree (`axios@1.20.0` under `@n8n/scan-community-package`, `axios@1.18.0` under `@n8n/backend-network`); 1.16.1 is in-range, and `package-lock.json` is gitignored, so a fresh install resolves it - the stale instance is a local dev-tree artifact only |
| `ip-address` | 8 GHSAs (`<=10.7.3`, installed 10.2.0) carried by `socks@2.8.9` <- `socks-proxy-agent` <- `pac-proxy-agent` <- `proxy-agent` <- `release-it`; in-range fixes exist (registry 10.7.5) and the same untracked-lockfile caveat applies |
| `nanoid` | 3 GHSAs (`<3.3.18`, installed 3.3.8) carried by `@n8n/utils@1.38.1` under `@n8n/node-cli` 0.39.3; npm's "fix" is the `n8n-workflow` downgrade to 1.75.1 |
| `brace-expansion` / `qs` | fixed within-major versions exist (5.0.12 / 6.16.1), but the only delivery mechanism (npm override) is forbidden by the gate; a global brace-expansion pin also breaks the scanner's nested `minimatch@3.1.5` (verified) |
| `release-it` chain | even 21.1.1 still pulls the undici / proxy-agent / basic-ftp chain; no published `release-it` escapes it |

Residual after the 2.5.1 dev-toolchain pins: the table above is the complete documented
set — verify current state with `npm audit` in the repo (the lockfile is gitignored, so
the set reflects the local install snapshot; a fresh install resolves the in-range fixes
listed above). The `handlebars` critical is carried by `@n8n/node-cli` 0.39.3 and is
fixed only in an n8n platform release; the exact pin keeps the build gate deterministic
on fresh installs.

All remaining flagged packages reduce to the documented leaf causes above — the axios,
`follow-redirects`, `ip-address` (release-it chain), `nanoid`/`@n8n/utils`, zod and uuid
leaves, the `release-it` chain, the `tmp` leaf, and the `handlebars` critical carried by
`@n8n/node-cli` — so the entire 38-residual set (2 critical / 32 high / 4 moderate) is
documented in this file. `package-lock.json` is gitignored, so this set is a local
dev-tree snapshot; a fresh install resolves the in-range fixes above.

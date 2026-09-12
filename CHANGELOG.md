# Changelog
All notable changes to this project will be documented in this file.

## [2.3.0] - 2026-09-12

### Changed
- **API alignment with Hudu 2.45.1** (reference: `api-docs-v2.45.1.json`). Activity Logs `resource_type` is now a server-side filter — selecting only *Resource Type* no longer pages through all logs and filters client-side (2.44.2-era workaround removed). `resource_id` still requires `resource_type` and is ignored on its own.
- Activity Logs `resource_id`/`resource_type` UI descriptions updated to the 2.45.1 semantics; README API compatibility line now **2.45.1**.

## [2.2.0] - 2026-08-08

### Added
- **Relations API-side filtering (requires Hudu API 2.44.1+).** The Relations Get Many operation now passes all filter parameters (`created_at`, `description`, `fromable_id`, `fromable_type`, `is_inverse`, `toable_id`, `toable_type`, `updated_at`) as native query parameters to the Hudu API, replacing the previous client-side filtering approach. This eliminates the performance penalty of fetching all relations and filtering locally. Filter dropdowns no longer display the 🐌 client-side filtering indicator.
- **Relations timestamp fields.** The `IRelation` interface now includes `created_at` and `updated_at` fields (ISO 8601), matching Hudu API 2.44.2.
- **Expanded relation record types.** The `fromable_type` and `toable_type` dropdowns now include all 11 API-supported record types: Asset, Website, Procedure, AssetPassword, Company, Article, Network, IpAddress, Vlan, VlanZone, and RackStorage (previously limited to 6).

### Removed
- Unused `LABEL_RECORD_TYPE_DESCRIPTIONS` and `RELATION_RECORD_TYPE_DESCRIPTIONS` constants. These exist to feed LLM tool descriptions in the full `n8n-nodes-hudu` package; this edition has no `ai-tools/` runtime, so nothing referenced them.

### Breaking
- **Relations filters now require Hudu 2.44.1 or newer.** The `/relations` filter query parameters were introduced in Hudu 2.44.1; the client-side filtering fallback that previously made these filters work on any Hudu version has been removed. On instances below 2.44.1 the API silently ignores the unknown query parameters, so Relations Get Many returns **unfiltered** results. Upgrade Hudu to 2.44.1+ before relying on these filters.

## [2.1.1] - 2026-07-22

### Fixed
- Best-effort enrichment lookups no longer swallow API errors silently. Five company/folder enrichment catches in the Articles and Folders handlers now log via `debugLog('[ENRICHMENT] … failed …', error)` before falling back to a default, matching the `[OPTION_LOADING]` pattern applied to the option loaders in 2.1.0. These remain non-fatal (a failed optional lookup still skips enrichment rather than aborting the operation) but are now traceable.

## [2.1.0] - 2026-07-22

### Fixed
- Removed the unsupported `subcategories` field from the node codex file (`HuduCore.node.json`). Only `node`, `nodeVersion`, `codexVersion`, `categories`, `resources`, and `alias` are part of the codex schema; the extra field was silently ignored by n8n. Flagged in n8n Creator Portal verification.
- Option loaders no longer swallow API errors silently. Six `loadOptions` handlers (`getAssetLayouts`, `getCompanies`, `getUsers`, `getGroups`, `getVlanZones`, `getAssetLayoutFieldValues`) now log the error via `debugLog('[OPTION_LOADING] Error …', error)` before returning an empty list, matching the existing pattern in `getLists` and `loadLabelTypes`. Misconfigured credentials or API failures are now traceable instead of surfacing as an empty dropdown with no diagnostic.

## [2.0.1] - 2026-07-20

### Changed
- Republished with **npm provenance** via GitHub Actions — required for n8n Cloud community-node verification (2.0.0 was published without provenance and was rejected by the Creator Portal). No functional changes from 2.0.0. The publish workflow uses `npm install` (this repo does not commit a lockfile).

## [2.0.0] - 2026-07-20

### Changed (breaking)
- **Package renamed `n8n-nodes-hudu-cloud` → `n8n-nodes-hudu-core`.** The old package is deprecated on npm; install `n8n-nodes-hudu-core` going forward.
- **Renamed the node and credential types so this edition can be installed alongside the full [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) package on the same n8n instance.** The node is now **"Hudu Core"** (internal type `huduCore`, file `HuduCore.node.ts`) and the credential **"Hudu Core API"** (internal type `huduCoreApi`). Previously both were `hudu` / `huduApi`, which collided with the full package — n8n keys installed node and credential types by name, so the two could not co-exist.
- **Breaking:** 1.0.0 (published as `n8n-nodes-hudu-cloud`) used the `hudu` / `huduApi` types; existing installs, workflows, and credentials will not carry over. Install `n8n-nodes-hudu-core`, add the **Hudu Core** node, and create a **Hudu Core API** credential. (1.0.0 had negligible installs.)

## [1.0.0] - 2026-07-18
Initial release. n8n Cloud–verifiable edition of the Hudu node, derived from n8n-nodes-hudu 2.7.0.

- Zero runtime dependencies, qualifying this package for n8n Cloud community-node verification.
- Includes the regular `Hudu` node with full resource/operation coverage (Companies, Assets, Articles, Passwords, Networks, VLANs, Users, and more).
- Retains `usableAsTool: true` on the `Hudu` node, so it can still be connected directly to an n8n AI Agent node — no separate tool node required.
- Does not include the dedicated `HuduAiTools` / MCP Server Trigger tool node. For that functionality, use [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) (self-hosted only).
- Get Many "Limit" fields default to 50, per n8n Cloud community-node verification rules.

"Cloud" in this package's name refers to **n8n Cloud** (the hosted n8n platform), not Hudu's own hosting.

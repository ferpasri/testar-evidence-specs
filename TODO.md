# Specification TODO

## Specification status

The working contract and shared cases use schema version 2, including explicit check targets, rules, and reported readings. The public v0.1.0 prerelease contains the earlier schema-version-1 baseline. A new prerelease is needed to distribute these changes to implementation tests.

## Foundation

- [x] Describe specifications -> language implementations -> generic data.
- [x] Define tool-adapter responsibilities and separate consumers from generation.
- [x] Define concrete recording independently of optional abstraction.
- [x] Explain capture limits, unknown values, and source-prefixed properties.
- [x] Define evaluation targets, rules, outcomes, and absent/null/unknown readings.
- [x] Provide shared cases for targeted checks, a widget-property sweep, reading distinctions, broader scopes, and invalid target references.

## Implementation alignment and release

- [ ] Review and publish the updated schema-version-2 prerelease.
- [ ] Align language implementations and adapters with the evaluation fields and reference validation.
- [ ] Extend implementation conformance runners for rejected cases and accepted-prefix comparisons.
- [ ] Run reader and producer cases against implementations and exchange their generated evidence.
- [ ] Establish stable release/versioning and migration rules before publishing a stable format.

## Shared contract

- [ ] Finalize minimum concepts and required versus optional capabilities.
- [ ] Publish JSON Schemas for manifests and events, including check-target variants.
- [ ] Specify full event ordering, lifecycle, concurrency, and reference validation.
- [ ] Specify replay/idempotency, errors, version compatibility, and incomplete files.
- [ ] Specify artifact paths/integrity, properties, numbers, and extension rules.
- [ ] Extend normalized expected results as additional record kinds and error cases are defined.

## Additional conformance cases

- [ ] Add repeated visits, self-loops, and multiple outcomes.
- [ ] Add rejected/incomplete action execution and repeated-event cases.
- [ ] Add widget hierarchy, candidate binding, capture-limit, and property-availability cases.
- [ ] Add invalid property target shapes, execution references, and sequence endpoints.
- [ ] Add replay conflicts, invalid versions, and corrupted/missing artifacts.

## Optional derived views and later capabilities

- [ ] Define derived-view identity, policy/version/configuration, input scope, and supporting evidence references.
- [ ] Add derived-view cases and keep grouping-algorithm quality tests with abstraction components.
- [ ] Specify query/export and abstraction rebuilding when consumers require them.
- [ ] Define service/viewer integration separately from the core data contract.
- [ ] Define matching/merging before accepting cross-source equivalence claims.

# TODO

## Implementation alignment and release

- [x] Publish the evaluation contract and shared cases in release v0.2.0.
- [x] Align current language implementations with evaluation fields and reference validation.
- [x] Verify reader and producer conformance, including rejected cases and accepted prefixes.
- [ ] Automate cross-implementation exchange of generated evidence, including artifacts and evaluation results.
- [ ] Establish stable release/versioning and migration rules before publishing a stable format.

## Foundation

- [x] Describe specifications -> language implementations -> generic data.
- [x] Define tool-adapter responsibilities and separate consumers from generation.
- [x] Define concrete recording independently of optional abstraction.
- [x] Explain capture limits, unknown values, and source-prefixed properties.
- [x] Explain discovered candidates, execution stages, and exploration scope.
- [x] Define evaluation targets, rules, outcomes, and absent/null/unknown readings.
- [x] Provide shared cases for targeted evaluations, a widget-property sweep, reading distinctions, broader scopes, and invalid target references.

## Shared contract

- [ ] Finalize minimum concepts and required versus optional capabilities.
- [ ] Publish JSON Schemas for manifests and events, including check-target variants.
- [ ] Specify full event ordering, lifecycle, concurrency, and reference validation.
- [ ] Specify replay/idempotency, errors, version compatibility, and incomplete files.
- [ ] Specify artifact paths/integrity, properties, numbers, and extension rules.
- [ ] Define consistent parser expectations for unknown fields, duplicate keys, numeric ranges, and resource limits.
- [ ] Extend normalized expected results as additional record kinds and error cases are defined.

## Additional conformance cases

- [ ] Add several discovered candidates with only one executed, retaining the unselected bindings.
- [ ] Add repeated visits, self-loops, and multiple outcomes.
- [ ] Add rejected/incomplete action execution and repeated-event cases.
- [ ] Add widget hierarchy, candidate binding, capture-limit, and property-availability cases.
- [ ] Add invalid property target shapes, execution references, and sequence endpoints.
- [ ] Add replay conflicts, invalid versions, and corrupted/missing artifacts.

## Optional derived views and later capabilities

- [ ] Define derived-view identity, policy/version/configuration, input scope, and supporting evidence references.
- [ ] Add derived-view cases for policy information and supporting evidence links.
- [ ] Specify query/export and abstraction rebuilding when consumers require them.
- [ ] Define service/viewer integration separately from the core data contract.
- [ ] Define matching/merging before accepting cross-source equivalence claims.

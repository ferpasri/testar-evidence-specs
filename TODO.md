# Specification TODO

## Specification status

The shared contract is under development. Schema version 1 identifies the current experimental encoding, not a stable specification release. The available initial-observation case covers its documented behavior; full conformance requires the additional cases and runner listed below.

## Foundation

- [x] Describe specifications -> language implementations -> generic data.
- [x] Define tool-adapter responsibilities and separate consumers from generation.
- [x] Organize cases by behavior and provide an initial-observation case.

## Concrete evidence and optional abstraction

- [x] Explain capture limits, unknown values, and source-prefixed properties with plain examples.
- [x] Separate concrete traces from optional derived views in the architecture and concepts.
- [ ] Define a concrete-only encoding: recording must not require abstract-state identifiers or an abstraction policy in its manifest.
- [ ] Define optional derived-view records: view identity, policy/version/configuration, input scope, and supporting evidence references.
- [x] Make the initial-observation inputs and expected results concrete-only: remove abstraction configuration, abstract-state identifiers, and derived graph expectations.
- [ ] Specify forward action/result links and repeated visits as distinct observations, with no automatic reverse links or state merging.
- [ ] Specify capture completeness/limits and missing/unknown property values, including namespaced extension properties.
- [ ] Extend concrete-only case coverage and add separate optional derived-view cases; keep grouping-algorithm quality tests with the abstraction component.

## Implementation alignment

- [ ] Align the Rust reader with the revised concrete-only case before claiming conformance. Its earlier experimental schema-1 representation requires the abstraction fields removed from this case. Set release/version and migration rules before publishing a stable format.

- [ ] Update the Rust recorder/domain/store after the shared format is defined: remove mandatory abstraction configuration and abstract-state identifiers from concrete recording.
- [ ] Update Rust queries, adapters, and tests to consume concrete traces and optional derived views separately.
- [ ] Document compatibility/migration for existing schema-1 evidence without losing original records or screenshots.

## Shared contract

- [ ] Review and publish a versioned specification release with compatibility guarantees.
- [ ] Finalize minimum concepts and required versus optional capabilities.
- [ ] Specify manifest/event fields and publish JSON Schemas.
- [ ] Specify identities, abstraction metadata, canonical encoding, and hash vectors.
- [ ] Specify event ordering, lifecycle, concurrency, and reference validation.
- [ ] Specify replay/idempotency, errors, version compatibility, and incomplete files.
- [ ] Specify artifact paths/integrity, properties, numbers, and extension rules.

## Conformance

- [ ] Define normalized expected results and the runner input/output contract.
- [ ] Add completed/repeated transitions, self-loops, and multiple outcomes.
- [ ] Add rejected/incomplete execution and explicit check-status cases.
- [ ] Add widget hierarchy, candidate binding, and capture-scope cases.
- [ ] Add replay/conflict, invalid references/versions, and corrupted/missing artifacts.
- [ ] Run cases against implementations and exchange cross-language producer output.

## Later capabilities

- [ ] Specify query/export and abstraction rebuilding when consumers require them.
- [ ] Define service/viewer integration separately from the core data contract.
- [ ] Define matching/merging before accepting cross-source equivalence claims.

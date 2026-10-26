# TODO

## Release and implementation alignment

- [x] Publish the shared contract, JSON Schemas, documents, and conformance cases.
- [x] Align Rust, Java, and Python public APIs, readers, and writers with the shared contract and verify the released cases.
- [ ] Align adapter dependencies and recording calls, retaining source-tool behavior and capture meanings.
- [x] Align dashboard import, labels, severity inspection, evaluation coverage, and abstract-model views.
- [ ] Decide how consumers handle historical evidence formats and define explicit conversion where required.
- [ ] Automate cross-implementation reading of generated evidence, including artifacts, metadata, and oracle results.

## Shared contract and conformance

- [x] Define the shared vocabulary for test runs, test sequences, recorded states, available actions, action descriptions, execution results, recorded transitions, and oracle results.
- [x] Define JSON Schemas, lifecycle rules, event identity, reference validation, and expected conformance summaries.
- [x] Add a multi-sequence test run, late run-level oracle result, and cross-sequence transition rejection.
- [x] Preserve existing capture, action, metadata, temporal-assessment, and property-evaluation cases under the agreed vocabulary.
- [x] Define optional source-defined oracle severity with omitted, label-only, numeric, and invalid-label cases.
- [ ] Extend malformed-record cases for duplicate keys, invalid UTF-8, unsupported numeric values, unknown fields, event conflicts, and resource limits.
- [ ] Add more lifecycle cases for interleaved sequences, duplicate sequence indexes, repeated finish events, and attempts after a history finishes.
- [ ] Add recorded-transition self-loops, multiple outcomes, and repeated-event cases.
- [ ] Add invalid property-target shapes, action-execution references, and observation-range endpoints.
- [ ] Add conflicting repeated-event cases, invalid schema versions, and missing or corrupted artifacts.
- [ ] Define consistent parser resource limits while retaining exact numeric values.
- [ ] Establish stable compatibility and migration rules before publishing a stable format.

## Capture and oracle results

- [x] Describe widget and property capture separately, preserving nested values, aliases, known omissions, and uncertainty.
- [x] Define temporal assessments, sequence/run phases, late reports, and preservation of pending oracle statuses.
- [ ] Define correlation between separate results when a producer needs an evolving evaluation history.
- [ ] Define portable source-oracle categories and visual annotations beyond the existing details map, with shared cases.
- [ ] Verify cross-tool comparisons using capture descriptions and original oracle targets.
- [ ] Add supplied sequence-level metadata cases alongside the existing run-level metadata cases.

## Capture comparison in abstract models

- [ ] Compare capture richness within observation groups selected by abstraction rules, showing each observation's widgets, properties, and recorded oracle results.
- [ ] Highlight data captured in another group member but absent from the selected capture as a potential capture gap, retaining supporting references and uncertainty.
- [ ] Distinguish uncaptured information, established absence, and no recorded evaluation. Missing capture alone does not prove the oracle never checked a property.
- [ ] Require explicit widget matching before comparing corresponding widget properties. Equal page addresses and titles do not establish equal content.
- [ ] Add examples with rich and limited captures, known exclusions, genuine content changes, and results for uncaptured properties.

## Offline query oracles

- [ ] Let users select original observations and widgets through an abstract model and evaluate conditions against their recorded properties.
- [ ] Start with captured image widgets and a condition requiring present, non-empty alternative text.
- [ ] Use each observation's own data; a richer group member does not supply a missing value to another observation.
- [ ] Define outcomes for satisfied conditions, violated conditions, and insufficient evidence, preserving the distinction between missing capture and established absence.
- [ ] Keep offline oracle results separate from original trace results. Record oracle identity/version/configuration, evaluation time, input identities, targets, observed values, and applied abstraction rules.
- [ ] Preserve original recorded evaluation coverage and offer a separate offline-results view.
- [ ] Use original recorded transitions for temporal questions; a path combining different test sequences does not establish one actual execution.
- [ ] Add cases for offline success, failure, insufficient capture, explicit absence, differing capture richness, and ambiguous widget matching.

## Abstract models and future capabilities

- [ ] Define a portable abstract-model format with abstraction rules, versions, selected evidence, and supporting references.
- [ ] Configure state and action abstraction separately while preserving recorded transitions.
- [ ] Add abstract-model cases for rule identity/configuration, input scope, and supporting evidence references.
- [ ] Specify evidence query/export and abstract-model rebuilding when consumers require them.
- [ ] Define service and viewer integration separately from the core contract.
- [ ] Define matching and merging rules before asserting cross-source equivalence.
- [ ] Add quality tests for abstraction components according to their testing objectives.
- [ ] Explore indexed storage and centralized evidence collection without changing captured source facts.

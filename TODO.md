# TODO

## Implementation alignment and release

- [x] Publish the evaluation contract and shared cases in release v0.2.0.
- [x] Align current language implementations with evaluation fields and reference validation.
- [x] Verify reader and producer conformance, including rejected cases and accepted prefixes.
- [ ] Publish the capture conventions, temporal assessments, and expanded shared cases as v0.2.1.
- [ ] Align language libraries, adapters, and consumers with the expanded shared cases and conventions.
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

## Capture and evaluation evidence

- [x] Describe widget and property capture separately, including nested values, aliases, limits, and known omissions.
- [x] Clarify temporal assessments and property evaluations, distinguishing open-ended conditions from assessments of a recorded interval.
- [x] Define reported assessments at completion, deadline, interruption, and failure, with shared examples preserving original statuses and scope.
- [ ] Define correlation between separately reported results when producers need an evolving evaluation lifecycle.
- [ ] Verify cross-tool coverage comparisons using the documented capture descriptions and original evaluation targets.

## Run metadata alignment

- [x] Define optional run origin and test objective descriptions, with separate adapter identity and version.
- [ ] Align language libraries, adapter APIs, and examples with the shared run metadata names. Preserve caller descriptions and record known adapter details automatically.
- [x] Add shared cases with metadata supplied and omitted, descriptive origin labels, and original text preserved. Include one origin that performs both predefined setup and exploratory actions.
- [ ] Display and filter the optional run descriptions consistently in consumers while retaining producer and run identities.

## Capture comparison in derived models

- [ ] Define a comparison view for observations grouped by user-selected matching rules. Show which widgets and properties each observation captured and which have recorded evaluation results.
- [ ] Highlight information captured in another observation in the group but missing from the selected observation as a potential capture gap. Retain the original records and link every comparison to its supporting observations and matching rules.
- [ ] Distinguish information not captured, a property explicitly recorded as absent, and an item with no recorded evaluation. Show reported capture selections, exclusions, and limits when available. Missing capture alone does not establish that an oracle never checked the item.
- [ ] Require explicit widget matching before comparing properties of corresponding widgets. Grouping observations by page address and title alone does not establish identical page content or corresponding widgets; application changes can explain differences.
- [ ] Add shared examples with richer and more limited captures, known exclusions, genuine content differences, and evaluation results for properties whose values were not captured. Use these to test that comparisons preserve uncertainty and original evaluation targets.

## Offline query oracles over captured evidence

- [ ] Define how users select observations, widgets, and properties through a derived model and apply test oracles after execution. Start with a concrete example: select captured image widgets and check whether their alternative-text property is present and non-empty.
- [ ] Evaluate each selected original observation using its own captured data. A richer observation in the same derived state can support comparison, but does not supply a missing value for another observation. Identify the state and widget matching rules used to select or relate records.
- [ ] Distinguish a satisfied condition, a violated condition, and insufficient evidence to decide. Missing capture alone does not establish application-level absence; a presence oracle needs evidence that absence was established. Define how insufficient evidence and evaluation errors are represented before adding result fields or statuses.
- [ ] Keep offline results separate from the original trace and distinguish them from results reported during execution. Record the oracle identity, version and configuration, evaluation time, input evidence identities, exact original targets, reported readings, and any matching policy used. Preserve links to supporting records.
- [ ] Explain offline results in terms of the captured moments. A derived-model path can combine different runs; use original execution links when an oracle evaluates a sequence, and do not treat unobserved intervals as established behavior.
- [ ] Add shared examples for passed and failed offline checks, insufficient capture, explicit absence, differing capture richness, and matching ambiguity. Specify that offline results do not change the original run's recorded evaluation coverage; consumers can offer a separate offline-results view.

## Additional conformance cases

- [x] Add several discovered candidates with only one executed, retaining the unselected bindings.
- [x] Add scripted action steps, repeated visits, rejected submissions, and incomplete executions.
- [x] Add widget hierarchy, candidate binding, capture-limit, and property-availability cases.
- [ ] Add self-loops, multiple outcomes, and repeated-event cases.
- [ ] Add invalid property target shapes, execution references, and sequence endpoints.
- [ ] Add replay conflicts, invalid versions, and corrupted/missing artifacts.

## Optional derived views and later capabilities

- [ ] Define derived-view identity, policy/version/configuration, input scope, and supporting evidence references.
- [ ] Add derived-view cases for policy information and supporting evidence links.
- [ ] Specify query/export and abstraction rebuilding when consumers require them.
- [ ] Define service/viewer integration separately from the core data contract.
- [ ] Define matching/merging before accepting cross-source equivalence claims.

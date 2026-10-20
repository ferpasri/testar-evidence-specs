# Generic Model Semantics

## Recorded evidence

| Record | Meaning |
| --- | --- |
| Run | One execution session, including which tool produced it and the relevant application/environment details. |
| Observation | One moment captured in a run. Returning to the same screen creates another observation. |
| Concrete state | What was captured at that moment: widgets, descriptive properties, and capture limits. |
| Widget | A captured UI component with an identity, optional parent, and properties such as label or enabled status. |
| Action definition | A recorded description of an operation that can be referenced by candidates and executions. |
| Candidate action | An action the tool generated or considered at an observation, optionally linked to a widget. |
| Action execution | One selected action occurrence, with its source observation, target, actual parameters, and submission/result information. |
| Check result | The recorded outcome and evidence from an evaluation, identifying its rule, concrete target, status, and any reported reading. |
| Artifact | Evidence stored as bytes, such as a screenshot, and referenced by an observation. |

A concrete execution link connects a source observation to the result of an action. It records an occurrence, rather than claiming that either screen is equivalent to another screen.

## Identity and capture

Identifiers distinguish records and allow them to reference each other. Producers include enough source/run information to avoid accidental identifier collisions. A widget identifier describes a captured component; it does not automatically match that component to one in another observation.

Complete, filtered, and unavailable capture have different meanings, explained in the [architecture](testar_state_model_architecture.md#capture-limits-and-extra-properties). An unknown enabled status is not `false`, and unavailable widget data is not an empty UI. Missing values and explicitly recorded null values remain distinct.

Extra properties can use a source prefix, such as `toolone.fingerprint` or `tooltwo.selector`. The prefix tells a consumer where the property comes from. Consumers can preserve unfamiliar properties without needing to interpret them; another producer can omit them.

## Discovered actions and exploration

An observation retains the candidate actions reported by its producer, including those never selected. Each candidate identifies an action definition and, when supplied, a widget at that observation.

The observation's `candidate_scope` describes that list:

| Value | Meaning |
| --- | --- |
| `generated` | The producer reports actions from its action-generation stage. |
| `eligible` | The producer reports actions it considers eligible under its selection rules. |
| `unknown` | The scope of the reported candidate list is unknown or was not supplied. |

For example, a tool can report ten candidates and execute one. The evidence keeps all ten candidate bindings and records the selected execution separately. An empty candidate list with unknown scope leaves the available actions unknown.

Consumers can identify candidates with no recorded selection from that observation. They can also distinguish selection, accepted submission, and a resulting observation. These answer different exploration questions.

Candidate lists describe what the tool reported. They do not establish every possible action in the application. Deciding whether a candidate was explored at another visit requires a policy for matching observations and actions.

## Action execution and trace direction

An initial observation has no preceding action execution. Selecting an action records its source and, when known, its candidate/target binding. Submission records whether the tool accepted the action; acceptance alone does not establish successful behavior in the application.

When an accepted execution is linked to a resulting observation, the trace records a forward link between those two occurrences. Separate runs do not get implicit links, and returning to the same page does not reuse an earlier observation.

Rejected or interrupted executions preserve what happened without inventing a result. Keeping two identical screenshots as one stored file is permitted; this storage optimization does not merge their observations or establish state equivalence.

## Optional derived views

| Concept | Meaning |
| --- | --- |
| Abstraction policy | Rules choosing which differences matter for an application, domain, testing objective, or analysis. |
| Abstract state | A policy's grouping of concrete observations. |
| Abstract action | A policy's grouping of recorded actions or executions. |
| Derived transition | A source-group/action-group/target-group relationship supported by concrete executions. |

Concrete recording does not require an abstract-state identifier or an abstraction policy. Those belong to optional derived views. A view records its own identity, policy/version/configuration, input scope, and supporting evidence references so different interpretations can coexist.

Repeated executions can support one derived transition. A derived view may contain cycles and several outcomes for the same source/action. Changing a grouping policy does not overwrite observations, executed parameters, screenshots, or check results. The generic recorder does not decide that equal URLs or images mean equivalent states.

## Check results and screenshots

Captured properties describe observations. A test oracle performs an evaluation by applying a rule to a target. A check result records its outcome and supporting evidence, identifying the target and a passed, failed, pending, unknown, or error status.

A test oracle applied to several widgets produces a separate evaluation for each widget/property target. Reported readings distinguish a JSON value, established absence, and unknown information. An explicit null remains a JSON value. A property with no matching check result has no recorded evaluation. Results for broader scopes retain those scopes rather than establishing property-by-property coverage.

[Evaluations and check results](check-evaluations.md) defines the terminology, record fields, target forms, reference rules, and reading representations.

The adapter captures screenshots and supplies them to the implementation. Stored references identify their relative paths, media type, and SHA-256 hashes, which readers can use to check that the bytes are unchanged. Several observations may reference the same image. Moving JSONL alone does not preserve the image bytes.

## Local data and replay

The local data layout uses `manifest.json`, `evidence.jsonl`, and `artifacts/`. The manifest identifies the format version and evidence collection. JSONL contains versioned events with stable identifiers. Optional derived views refer back to that evidence without making grouping a condition of recording.

The current format uses `schema_version: n` in the manifest and every event envelope. This number identifies the data format, while release tags identify published versions of the specifications and shared test data.

Reprocessing an identical event should not duplicate records. Conflicting content for an existing event identifier should be rejected. File locks and internal graph representations are implementation details rather than shared model data.

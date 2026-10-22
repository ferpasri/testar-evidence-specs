# Recorded evidence and its meaning

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

A tool can capture every available widget while saving only some of each widget's properties. For example, a size limit can exclude one widget's long description while retaining that widget's role and text. [Capture and properties](capture-evidence.md) defines optional descriptions of those choices, known omissions, nested values, and aliases.

## Common descriptive properties

Adapters can use these optional state-property names when the information is available:

| Property | Meaning |
| --- | --- |
| `ui.url` | The full address captured for the current page, including its path parameters, query, and fragment. |
| `ui.title` | The page, screen, or window title captured from the application. |

These conventions use captured strings. A page can have an empty title; keeping `""` preserves that information. When the title or address is unavailable, the adapter describes that capture limit rather than inventing a value. Desktop and other applications can supply a title without a URL.

Read a page title from the application's document-title information and a page address from its current-address information. Preserve the returned values, including empty strings.

Widget properties need their own definitions. An HTML `title` attribute, visible text, and an accessible name describe different things. A tool's property named `Title` may represent any of these. Keep its original name and document what it contains.

A common property name must describe a concrete property or API value that the source actually provides. The adapter verifies that meaning before adding the common name. A widget title is recorded when an actual title property is available; text or an accessible name is not substituted for a missing title. The state-level `ui.title` convention above describes a page, screen, or window title; it does not define a generic widget title.

Adapters preserve captured facts and use documented conversions for their JSON representation. Capture descriptions, generated identifiers, and alias mappings describe the recording itself. Decisions such as which widget is most important or which screens are equivalent belong in separately identified analysis results.

Keep the source-specific properties alongside these common names when available. Consumers can use the common names across producers while retaining the original descriptions. A consumer may normalize addresses or titles for a derived comparison; the recorded values remain unchanged.

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

## State-action-state execution traces

An initial observation has no preceding action execution. Selecting an action records its source and, when known, its candidate/target binding. Submission records whether the testing tool's runner, driver, or execution wrapper accepted the action. The application's response is recorded in the resulting observation.

Each completed action connects the observation before the action to its resulting observation, forming a state-action-state execution trace. Separate runs do not get implicit links, and returning to the same page does not reuse an earlier observation.

A scripted helper records the steps that the testing tool reports. Separate username entry, password entry, and submission can each have their own source observation, execution, and result. If the tool reports one compound action, that action has one recorded execution with its supplied parameters. A further capture without a reported action remains a separate observation.

A rejected submission means the testing tool reports that it could not accept or execute the action, for example because the target widget disappeared. Any reported error is saved. No observation is attached as the completed result of that rejected execution; later captures can still be saved as separate observations.

If a click is executed and the application then displays "Incorrect password", the click has a resulting observation containing that response. This is an application response to an executed action.

Finishing a run marks outstanding selected or accepted executions incomplete. Completed executions retain the result observation actually reported.

Keeping two identical screenshots as one stored file is permitted; this storage optimization does not merge their observations or establish state equivalence.

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

A test oracle applied to several widgets produces a separate evaluation for each widget/property target. Reported readings distinguish a JSON value, established absence, and unknown information. An explicit null remains a JSON value. A property with no matching check result has no recorded evaluation. A result about a whole page or run describes that page or run. Individual property coverage comes from results naming the particular properties.

Temporal evaluations describe conditions over a sequence of visits. An end-of-run report records the oracle's assessment and the interval it concerns. An open-ended condition can remain pending after execution stops. Each reported result remains a separate record.

[Evaluations and check results](check-evaluations.md) defines the terminology, record fields, target forms, reference rules, readings, and temporal assessments.

The adapter captures screenshots and supplies them to the implementation. Stored references identify their relative paths, media type, and SHA-256 hashes, which readers can use to check that the bytes are unchanged. Several observations may reference the same image. Moving JSONL alone does not preserve the image bytes.

## Local data and replay

The local data layout uses `manifest.json`, `evidence.jsonl`, and `artifacts/`. The manifest identifies the format version and evidence collection. JSONL contains versioned events with stable identifiers. Optional derived views refer back to that evidence without making grouping a condition of recording.

The current format uses `schema_version: n` in the manifest and every evidence event. This number identifies the data format, while release tags identify published versions of the specifications and shared test data.

Examples and adapters can create trace directories named `<sut>_<objective>_trace-<timestamp>`, for example `parabank_denied-loan_trace-2026-10-22_15-30-12-123`. Use labels supplied by the caller and a documented local or UTC time convention. Make labels safe for filenames and keep the original descriptions in recording metadata when supplied. Reserve the directory before writing; add a suffix such as `-2` when a name already exists. A sequence number can also be included. Explicit output paths remain caller choices. Readers use the manifest and event identities, so directory names do not define trace identity or state equivalence.

An evidence event carries its identity, format version, event kind, and reported data. Implementations can use the name `EvidenceEvent` for that structure; this naming does not change the JSON fields.

Reprocessing an identical event should not duplicate records. Conflicting content for an existing event identifier should be rejected. File locks and internal graph representations are implementation details rather than shared model data.

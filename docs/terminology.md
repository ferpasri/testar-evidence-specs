# Shared terminology

These terms describe what testing tools record and how consumers can build abstract models from that evidence. A test run can contain several test sequences. Each sequence keeps its own observations and action executions.

## Recorded execution evidence

| Term | Meaning | Example or distinction |
| --- | --- | --- |
| Evidence trace | Saved records of what was observed, selected, executed, and evaluated during testing. | A portable directory contains a manifest, an evidence log, and referenced artifacts. It can contain several test runs and sequences. |
| Test run | One invocation of testing, which can execute several test sequences. | Starting a tool configured for five sequences is one test run containing five test sequences. |
| Test sequence | An ordered series of observations and action executions, from its initial observation until it ends. | Each sequence has its own history; the next sequence starts a new history. |
| Session | The lifecycle of a tool, browser, or connection. | A browser session can be reused across test sequences. Session lifecycle does not define evidence grouping. |
| Observation | One recorded moment in a test sequence, including its recorded state, available actions, and artifact references. | Returning to the same screen produces another observation. |
| Recorded state | The application properties, widgets, and capture scope collected by the tool and saved for an observation. | Identical recorded-state data does not make two observations the same occurrence. Recording does not imply every widget or property was captured. |
| Widget | A captured UI component with properties and an optional parent. | A button or input field. Its identifier alone does not establish identity across observations. |
| Property | A named captured value. | Page title, text, bounds, or enabled status. A tool using typed tags translates them into named JSON properties while preserving their source meanings. |
| Action description | A reusable description of an action, including its identity, kind, and descriptive properties. | Typing into a text field. Actual generated input belongs to its execution. |
| Available action | An action the tool reports as available at an observation, optionally linked to a widget. | Typing into Username or activating Login. Availability follows the tool's discovery and filtering rules and does not guarantee successful execution. |
| Action execution | One selected action occurrence, its actual parameters, reported execution result, and any resulting observation. | Typing `john` into Username on one particular occasion. |
| Compound action | One tool-level action made up of several operations, recorded as one execution. | Typing into a field and pressing Enter. Its internal operations need not have separate execution records. |
| Action execution result | What the runner or wrapper reports about the action attempt, including an execution error when one occurs. | A normal return and an execution error are distinct. Neither alone determines whether an application response satisfies an oracle condition. |
| Recorded transition | An executed action connecting its source observation to its resulting observation. | One state-action-state occurrence. An execution without a resulting observation has no completed recorded transition. |
| Evidence Event | One reported occurrence in the evidence log, with identity, format version, event kind, and data. | An observation was recorded or an oracle produced a result. |
| Artifact | Stored bytes referenced by the evidence. | A screenshot. Moving the log alone does not move referenced image bytes. |

## Test oracles

| Term | Meaning | Example or distinction |
| --- | --- | --- |
| Oracle condition | What must hold for the check to pass. | The loan-status text must equal `Denied`. |
| Test oracle | The check that determines whether the oracle condition holds. | Code reading the loan-status text and comparing it with `Denied`. The condition and mechanism can appear together in one assertion. |
| Oracle evaluation | One application of a test oracle to a target. | Running the check after a particular loan request. Applying it again produces another evaluation. |
| Oracle result | The recorded outcome and supporting evidence from an evaluation. | Passed with expected and observed values both equal to `Denied`. |
| Oracle target | What the oracle evaluates. | A test run, test sequence, observation, action execution, widget, property, or observation range. A broad target does not establish that every property was checked. |
| Oracle status | Passed, failed, pending, unknown, or error, as reported by the oracle. | Failed means the condition was not satisfied. Error means the evaluation encountered a problem. |
| Oracle severity | The optional importance assigned by the source tool or oracle to a finding. | A failed condition can have severity `warning` or `critical`. Preserve source labels and any numeric values without inferring a default severity or universal scale. |
| Expected value | An optional value used by the oracle to determine whether its condition holds. | `Denied`. An omitted expectation differs from a supplied JSON null. |
| Observed value | Information obtained by the oracle: a value, established absence, or unknown information. | `Approved`, an attribute established as absent, or a value that could not be obtained. Null is a value, distinct from absence and unknown information. |
| Evaluation coverage | Which targets have recorded oracle results and their reported statuses. | A property has no recorded evaluation. This does not prove that no oracle ran; reporting may be incomplete. |

An oracle result identifies its oracle and any supplied version and configuration. A separate object representing the condition is unnecessary when the condition is explained by the result's name and the oracle's documented behavior. The observed value can differ from a separately captured recorded-state property because they can be read at different moments.

## Capture and available actions

| Term | Meaning | Example or distinction |
| --- | --- | --- |
| Capture scope | Whether widgets available from the capture source in the stated area were captured completely, selectively, or could not be captured. | Complete widget capture can still save only selected properties. Property selection and known limits are described separately. |
| Property alias | Another name for the same captured property within one recorded state or widget. | An adapter saves the same document title under `source.document.title` and `ui.title` and declares their relationship. Equal values alone do not establish an alias. |
| Available-action scope | Which stage of action discovery and filtering the reported list represents. | Generated actions, eligible actions, or an unspecified scope. |

| Available-action scope | Meaning | Example |
| --- | --- | --- |
| Generated | Actions generated before selection filters are applied. | Discover and record 20 actions. |
| Eligible | Actions offered to the selector after applying rules and filters. | Discover 20, exclude eight, and record the remaining 12. |
| Unknown | The integration does not specify which list it reports. | Record 12 actions without identifying whether others were filtered out. |

Property aliases apply within one record. They do not match properties across tools. Cross-source comparison uses explicit abstraction rules.

## Consumers and abstract models

| Term | Meaning | Example or distinction |
| --- | --- | --- |
| Abstraction rules | Rules deciding which differences matter when grouping observations, action descriptions, or executions. | Compare page titles and addresses while removing a session token for that comparison. |
| Abstract state | Observations grouped according to abstraction rules. | Several observations share the same normalized address and title. Original observations and recorded states remain available. |
| Abstract action | Action descriptions or executions grouped according to abstraction rules. | Typing different usernames into corresponding fields becomes one abstract action. |
| Abstract transition | A relationship from a source abstract state, through an abstract action, to a target abstract state, supported by recorded transitions. | Several executions from different sequences can support one abstract transition. |
| Abstract model | Abstract states, actions, and transitions built from evidence using explicit abstraction rules. | Each part retains references to the records supporting it. State grouping and action grouping can be implemented separately. |
| Observation-order link | A display connection between consecutive observations in one test sequence when no recorded transition connects them. | It establishes their recorded order, without claiming an action occurred or identifying the cause of any state difference. |

For example, observations before and after preparation code can have an observation-order link when its actions were not reported. That link does not invent a login action or become an abstract transition. Abstraction remains separate from the recorded traces.

## Qualified names and lifecycle

| Context | Terms | Meaning |
| --- | --- | --- |
| Recorded transition | Source observation / resulting observation | Particular recorded moments before and after an action. |
| Action | Action target / target widget | What the action operates on; use target widget for a widget reference. |
| Oracle evaluation | Oracle target | What the oracle evaluates. |
| Abstract transition | Source abstract state / target abstract state | The states connected by an abstract transition. |
| Imported evidence | Evidence source | The imported collection. |
| Producing tool | Source tool | The tool supplying observations and execution information. |
| Pending oracle status | No definite oracle outcome when reported. | The recorded status remains pending even if execution has ended. |
| Awaiting execution result | The runner has not reported the selected action's outcome. | After a normal return, it can separately await the resulting observation. |
| Buffered observation | A capture temporarily held in memory before writing. | An implementation step, independent of oracle status. |

Reconstructing evidence means reading saved Evidence Events and rebuilding their records and relationships in memory for validation and inspection. It preserves recorded facts rather than creating an abstract model.

See [run metadata](run-metadata.md) for origin, producer, adapter, and test objective, and [the evidence format](evidence-format.md) for record fields.

# Adapter gaps and considerations

The state model can only contain evidence that the testing tool and its adapter provide. A recording may follow the shared format correctly while still missing useful actions, widget properties, or test results.

This guide explains where those gaps can appear, how to reduce them, and what work remains for people or agents implementing adapters.

## Who does what?

| Part | What it does |
| --- | --- |
| Testing tool | Interacts with the application, captures its UI, and runs test oracles. |
| Adapter | Takes the information reported by the tool and translates it into evidence records. |
| State-model library | Validates, saves, and reads the supplied records and screenshot files. |
| Dashboard | Shows the recorded evidence and supports analysis of it. |

The specifications define the shared meaning of those records. The library and dashboard depend on the tool and adapter to supply the details needed for analysis.

## Recording scripted and exploratory actions

A scripted test follows steps chosen by its author. An exploratory or scriptless tool discovers possible actions and selects what to do while testing. Both can produce the same kinds of evidence: observations, available actions, and executed actions.

For example, a script signs in by entering a username, entering a password, and clicking Login. If the tool reports these operations separately and captures the UI between them, the trace can show:

```text
Observation 1 -> Enter username -> Observation 2
Observation 2 -> Enter password -> Observation 3
Observation 3 -> Click Login    -> Observation 4
```

If a helper reports only one "Sign in" action, the recording contains that one execution. Recording its internal steps requires the tool to report them and capture any intermediate observations.

An exploratory tool might discover five actions on a page and select one. The adapter preserves all five reported available actions, including the four that were not selected. It also records whether the list came from action generation, selection rules, or an unknown stage. Those available actions describe what the tool discovered.

**Where gaps appear:** An adapter may record the main exploration loop but miss scripted login, cleanup, direct driver calls, or custom methods. Even an exploratory test may use a script to reach its starting screen.

**Integration approach:** Connect recording to shared methods through which actions are executed. Start recording before the interactions that should appear in the trace.

**Work for implementers:** Find the execution paths used by the tool and its extensions. Connect missing paths, decide how detailed action recording should be, and test actions rejected by the tool's execution mechanism, retries, and interrupted test sequences.

## Choosing which properties to save

A widget can have text, a role, an enabled flag, and additional tool-specific properties. Some values are simple; others contain a collection of attributes:

```json
{
    "source.label": "Search",
    "source.attributes": {
        "placeholder": "Search products",
        "maxlength": "40"
    }
}
```

**Where gaps appear:** A mapper may save strings and numbers but discard useful collections. A browser adapter may read only a short list of attributes. A small list of common property names can also be misunderstood: it may only rename selected properties while other source properties are saved separately.

**Integration approach:** Preserve the available values that can be represented in JSON, including nested collections where supported. Add converters for useful tool-specific types. Describe which properties are collected, excluded, shortened, or unavailable, and explain size limits and sensitive-value handling.

"All properties" needs a clear boundary. Capturing every property exposed by the tool still depends on what that tool collected from the application. A complete widget capture means complete within its stated area, such as the current document.

Where available, use the [common state-property names](evidence-semantics.md#common-descriptive-properties) for the captured full address and page, screen, or window title. Read the state title from the page, screen, or window's title information. For widgets, preserve the actual source property: visible text, an accessible name, and a title attribute are different properties. A tool property called `Title` is not enough to establish that it contains an actual widget title.

Use the [capture conventions](capture-evidence.md) to describe the captured area, widget and property selection, limits, known omissions, and duplicated aliases. Preserve nested values, and use an addressable property key when an oracle reports an evaluation of one particular value.

**Work for implementers:** Check the meaning of each source property before assigning a common name. Document where its value comes from and any conversion. Keep the original names and values, and leave unavailable properties unknown. Review these mappings when the source tool changes.

## Recording what test oracles evaluated

A test oracle decides whether a condition is satisfied. An evaluation is one application of that oracle; a oracle result records its outcome.

Consider two examples:

- A scripted test performs ten actions and makes two assertions about widget properties. It records the two evaluations actually performed.
- An exploratory tool captures a page containing eight images and applies an alternative-text oracle to each image. It records eight results, identifying each image, the property evaluated, and the reported outcome.

Both successful and failed evaluations matter. Capturing a widget property does not mean an oracle evaluated it.

**Where gaps appear:** Detailed reporting may exist for only one oracle. Other oracles may produce only messages such as "page validation failed." Such a message does not identify every evaluated widget, property, or successful result. Collecting all messages centrally still leaves that detail unavailable.

**Integration approach:** Give the tool a shared way to report evaluation details. Oracles or common assertion helpers supply the oracle, target, outcome, and available expected/actual values. The adapter translates those reports into oracle results. An oracle evaluating a whole page keeps that broader target.

**Work for implementers:** Connect the shared reporting mechanism and update oracle code or assertion helpers where details are missing. This initial work cannot always be automated: the adapter cannot reliably reconstruct evaluations from message text.

A property missing from saved data may have been excluded during capture. Record absence when the oracle established absence. Preserve unknown or pending results as reported, including when the test sequence ends.

Temporal oracles report a condition over several visits. Preserve that scope, the oracle's interpretation of time, and any end-of-run assessment described in the [evaluation contract](oracle-results.md). Producing those assessments belongs to the testing tool and its oracle; the adapter translates the reports they supply.

## Keeping observations and screenshots together

After clicking Submit, the application may show a loading indicator before displaying a confirmation. Either moment can be recorded, but the saved properties and screenshot should describe the same visit.

**Where gaps appear:** Screenshots may be saved later, properties may be added after the adapter copied the state, or a mutable UI object may change before its data is saved.

**Integration approach:** Choose and document when observations are captured. Preserve their data and associated image bytes before later changes. Waiting for the application to settle is a testing-tool decision; a screenshot alone does not establish that loading finished.

**Work for implementers:** Check capture timing, custom state-building methods, delayed screenshot saving, and widget references. Test repeated visits as separate observations, even when they look identical.

## How to verify an adapter

Shared conformance tests check whether supplied evidence follows the format. Adapter tests check whether the expected evidence was collected.

Use small scripted and exploratory tests to verify the examples above: login steps, unselected available actions, nested properties, multiple oracle types, passed and failed evaluations, and matching screenshots. Also test missing captures, limits, errors, and custom execution paths.

Implementing an adapter therefore includes understanding the source tool, connecting its action and oracle reporting, converting data, testing real flows, and maintaining those connections. Shared hooks reduce repeated work as new actions and oracles are added.

Each adapter should briefly explain what it captures and its known gaps. Tool-specific code and tests stay with that adapter. Shared data requirements belong in these specifications.

See the [architecture](architecture.md), [model semantics](evidence-semantics.md), and [evaluation contract](oracle-results.md) for the record definitions.

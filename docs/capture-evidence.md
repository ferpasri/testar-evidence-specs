# Capture and property evidence

A recorded state records what a testing tool and its adapter collected at one observation. Its widget list, properties, and capture description help a reader understand how much of the application is represented.

## Widget capture and property capture

The state's `capture` field describes the widget tree:

| Value | Meaning |
| --- | --- |
| `complete` | All widgets available from the capture source within the stated area were retained, without a known filter or limit cutting off the tree. |
| `filtered` | The producer collected a selected portion, for example interactive widgets or a limited number of widgets. |
| `unavailable` | The producer could not collect widget data. An empty widget list in this case leaves the UI unknown. |

A tool can capture every available widget while saving only selected properties. The capture description identifies which properties are collected and, if known, explains exclusions or limitations.

The capture configuration chooses which available properties to save. Their names keep their source meanings, and their values come from the application or the source API. Common names are added only when the adapter has verified that they mean the same thing as the original property. See [captured property meanings](evidence-semantics.md#common-descriptive-properties).

Captured JSON values include empty strings, nulls, booleans, numbers, objects, and lists.

## Captured widgets and visibility

A widget can be present in the captured data while hidden on screen. Its presence records what the source collected, rather than whether it was shown to the user.

CSS `display` and `visibility` describe different aspects of rendering. An element with `display: none` does not generate a layout box, and its descendants are not shown. A child can report `visibility: visible` while an ancestor with `display: none` still hides it. A widget's own style values alone therefore do not establish whether it is shown.

Keep captured styles under their source property names, such as `source.style.display` and `source.style.visibility`, and retain the widget parents. A visible flag reported by a source API keeps that API's meaning. Describe how it is obtained: it may consider ancestor styles and element size while saying nothing about whether the element is inside the viewport or covered by another window or element. Missing visibility information remains unknown.

Comparing captured styles is different from filtering hidden widgets out of an abstraction. See the [hidden result-panel example](examples.md#hidden-result-panel).

The capture description states whether hidden elements are included and which style properties or visibility readings are collected. Adapters retain those readings; abstraction rules decide which captured differences matter for a testing objective.

## Describing the capture

The following optional properties provide a common way to describe capture decisions. They belong in the existing properties maps and are preserved as JSON values by evidence implementations.

A state's `evidence.capture` property contains:

| Field | Meaning |
| --- | --- |
| `area` | Readable description of the application area covered, such as the current application window. |
| `widget_selection` | Readable description of which widgets the producer selected. |
| `property_selection` | Readable description of which properties the producer selected. |
| `limits` | Object with named capture limits. Each name or explanation identifies what is limited and its unit, such as widgets or characters per value. |
| `truncated` | Boolean indicating whether a known limit caused information to be cut off. Omit this field when that is unknown. |

For example, `"limits": {"widget_count": 2000, "attribute_value_characters": 1000}` describes a limit of 2,000 widgets and 1,000 characters per attribute value. A configured limit alone does not establish that any data was cut off.

A state or widget can describe omitted properties in `evidence.omitted_properties`. Its value is a list of objects with a literal property key under `property`, a `reason`, and an optional readable `explanation`:

| Reason | Meaning |
| --- | --- |
| `excluded` | The capture configuration intentionally leaves out this property. |
| `unsupported` | The capture source does not provide this property. |
| `unavailable` | The producer could not obtain this property on this observation. |
| `limit` | A capture limit prevented retaining this property. |
| `redacted` | The producer intentionally removed the property to protect sensitive information. |

These entries describe known omissions. Producers can describe a broad selection in `evidence.capture` without listing every possible excluded property. A known omission leaves the property's value or presence unknown in the captured data. A test oracle can separately establish absence and report an `absent` observed value.

## Property keys, nested values, and aliases

A property target names an exact top-level key in a state or widget properties map. The dots in `ui.alt` are part of that key. An object stored under `source.attributes` is one captured property, even when it contains several attribute values.

To record an evaluation of an individual attribute, give it its own property key, such as `ui.alt`. Save the captured value under that key. A result reporting established absence or unknown information can still name a missing key. A result targeting the whole `source.attributes` object describes an evaluation of that object.

An alias is a second name for the same captured property. The adapter implementer declares it after verifying what the source property means. Equal values alone are insufficient: a width and a height both equal to 32 are still different properties.

The optional `evidence.property_aliases` object maps an additional name to the original property key within one state or widget record. Both names refer to the same source property and preserve its JSON value. For example, an adapter reads an image's alternative-text attribute as `source.alt` and also records it as `ui.alt`:

```json
{
    "source.alt": "",
    "ui.alt": "",
    "evidence.property_aliases": {"ui.alt": "source.alt"}
}
```

Here, the adapter declares `ui.alt` as another name for `source.alt` on this one image. It is not declaring that properties or widgets from different tools match. A viewer or analysis tool makes cross-source comparisons using its explicit abstraction rules.

Check results keep the property key that was actually evaluated. Recording an alias does not create another evaluation.

## Reading capture and evaluation counts

A captured property without a matching oracle result has no recorded evaluation. Descriptive capture properties are metadata about the evidence; they do not represent additional application features evaluated by a test oracle.

Raw counts of top-level keys depend on how producers represent their data. A nested object counts once, and an original property plus its alias creates two keys describing one value. A consumer comparing capture or evaluation coverage states how it treats metadata, aliases, and nested values, and retains access to the original records.

[Capture properties](../conformance/cases/capture-properties/README.md) demonstrates these distinctions with a complete tree, a limited tree, and unavailable widget data. [Oracle results](oracle-results.md) explains observed values and target-level coverage.

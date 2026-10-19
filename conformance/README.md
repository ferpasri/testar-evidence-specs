# Conformance Data

Cases contain generic evidence and expected results. Every language implementation uses the same cases, independently of the testing tool that supplied observations.

## Case layout

```text
cases/<behavior>/
    README.md
    manifest.json
    evidence.jsonl
    expected.json
    artifacts/          # When referenced by evidence
```

The manifest, events, and referenced artifacts are inputs. `expected.json` describes the records or error expected after processing them. It is test metadata; model producers do not have to generate that file.

An implementation's test harness loads inputs through its reader or API and compares the result with the expected records. Producer tests generate equivalent evidence and check its output. Observation order and widget hierarchy order matter; JSON object-key order and whitespace do not.

## Concrete traces and derived views

Concrete trace cases check observations, action occurrences, results, capture limits, checks, and artifacts. Recording evidence does not require running an abstraction algorithm or supplying abstract-state identifiers.

Optional derived-view cases check policy information and links to the original evidence. A grouping algorithm's own tests determine whether its interpretation is appropriate for its application domain or testing objective.

## Cases

- [Initial observation](cases/initial-observation/README.md): concrete evidence containing an open run, one observation and its concrete snapshot, one action definition, a captured widget, one candidate, and a screenshot reference.

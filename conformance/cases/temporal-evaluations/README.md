# Temporal evaluations

Four short runs preserve assessments made during execution and after it ends. Each run has two observations and three check results.

| Run | Ending reason | Recorded results |
| --- | --- | --- |
| Normal | `normal completion` | An open-ended condition is pending during and after the run. A separate rule passes because neither recorded observation contains a warning. |
| Deadline | `execution deadline reached` | The open-ended condition remains pending. A rule over the recorded interval reports unknown because the second warning reading is unavailable. |
| Interrupted | `user interruption` | The open-ended condition starts pending. A warning establishes failure during execution, and a separate end-of-run report preserves that failure. |
| Tool failure | `tool failure: application connection lost` | The condition starts pending. The evaluator reports its own error during execution, and the end-of-run assessment is pending. |

The open-ended rule asks whether a warning never appears. A run ending without an observed violation leaves that rule pending. The finite rule asks a different question: whether the warning property is false at every recorded observation within the named interval. Its parameters define how unavailable readings affect that assessment. The deadline run's captured null is preserved, and the evaluator cannot use it as a boolean reading.

`details["evidence.assessment"]` describes each report's phase and temporal meaning. End-of-run reports preserve the exact recorded ending reason. Each report has its own event ID and contributes to the result count. All temporal targets are run or sequence targets, so these results do not establish property-by-property evaluation coverage.

The accepted result contains **28 events, 4 finished runs, 8 observations, and 12 check results**: 7 pending, 1 passed, 2 failed, 1 unknown, and 1 error. There are no selected actions or artifacts. The error result describes an evaluator problem; the tool failure is preserved separately as the run's ending reason.

Read the events in order and compare the result with `expected.json`.

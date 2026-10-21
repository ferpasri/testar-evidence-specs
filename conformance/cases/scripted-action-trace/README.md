# Scripted Action Trace

A scripted sign-in fills an account name, fills a passcode, and activates the submit button. Each completed action records its source observation, accepted submission, and resulting observation. Parameters preserve the supplied strings, booleans, and numbers, together with descriptive origin and phase information.

The first three observations contain the same captured form structure and labels. Their observation and snapshot IDs remain distinct. The entered synthetic values are carried in the executions; this filtered capture records the form's structure and labels.

After sign-in, a continuation action is rejected because its target became unavailable. A subsequent refresh is accepted, but the run ends before its resulting observation is captured. The refresh becomes incomplete, while the rejected action retains its rejection and error.

Candidate scope is unknown: the listed bindings support these scripted selections without claiming a generated or eligible action inventory.

Read the events in order and compare the result with `expected.json`.

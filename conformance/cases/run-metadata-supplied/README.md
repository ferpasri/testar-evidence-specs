# Supplied run metadata

One run supplies all four [run metadata properties](../../../docs/run-metadata.md), together with an application label. Its origin contains leading and trailing spaces, mixed capitalization, and accented text. The test objective also includes Unicode text. Preserve every string exactly.

The same origin opens the loan feature during setup and then performs an exploration action. Both executions belong to the same run. Their supplied `phase` parameters describe the steps; the origin remains the source name recorded on the run.

The case contains thirteen events, three observations, and two completed executions. The captured result says Denied. There are no recorded test-oracle evaluations: the objective describes intent, and the captured text alone does not report a performed check.

Compare the summary with `expected.json` and compare complete accepted events with the input. In particular, verify all run properties after recording and reopening; the summary lists run IDs and status rather than repeating their properties.

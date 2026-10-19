# Initial Observation

This valid-input case uses schema version 1.

Load `manifest.json`, process `evidence.jsonl` in order, and check the referenced screenshot. The run starts, an `activate` action is defined, and one filtered observation contains a Login button with that candidate. The synthetic 1x1 PNG checks image storage and integrity, not whether a real page was rendered correctly.

The case expects one open run, one observation, one concrete state snapshot, one action definition, and no executed actions. The observation references `example:snapshot:1`; the snapshot describes the captured widgets and properties. The run remains open intentionally.

Compare the concrete records with `expected.json`.

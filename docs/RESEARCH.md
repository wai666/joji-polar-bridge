# Research notes

**English** · [简体中文](RESEARCH_CN.md)

> [!NOTE]
> Claim labels: **Verified** = supported by recorded project evidence; **Observation** = directly observed but scope-limited; **Hypothesis** = working interpretation needing more tests; **Policy** = JOJI-imposed rule.

## Device family

**Observation.** The current Polar Loop Gen 2 research target presents identifiers associated in this project with the newer TRIUMPH / `Polar_INW6F` platform rather than the legacy LOOP / LOOP2 family.

**Hypothesis.** That platform distinction may explain why behavior inferred from older Loop generations does not always transfer cleanly. It should not be treated as a vendor compatibility statement unless independently documented.

## PMD / real-time work

**Verified — project scope.** Earlier JOJI phases recorded successful online streams for skin temperature, PPI, accelerometer and PPG under a deliberately restricted concurrency policy.

This verifies the tested JOJI path and device state; it does not imply that every firmware/app combination exposes identical behavior.

## Device-side file lifecycle

**Observation.** Differential experiments recorded materially different lifecycle patterns across device files and SDK data:

- HR / PPI / AUTOS / sleep-intermediate artifacts changed or rolled across some sync windows;
- activity-sample and daily-summary data persisted differently in the observed windows;
- history/index files could update without matching the lifecycle of queue-like datasets.

**Hypothesis.** Some of the changing datasets may behave like rolling or consumable sync queues.

> [!IMPORTANT]
> The lifecycle interpretation above is a research hypothesis derived from JOJI evidence. It is **not** a claim about an undocumented Polar contract or a guarantee across devices, firmware versions or Flow versions.

## Current transient to investigate

**Observation.** A3 evidence captured a RAW inventory failure with protobuf parse error `invalid tag (zero)`. A later run succeeded.

**Hypothesis.** The failure is transient or stage-local rather than proof that the underlying dataset is permanently unreadable.

**Planned validation:**

- isolate inventory failure to its stage;
- preserve already committed local data;
- avoid restarting unrelated completed work;
- make retry/recovery evidence explicit;
- correlate RAW path changes over time.

## USB research branch

**Policy.** The PC-USB branch remains passive-first. Public research is limited to enumeration/interface inspection before considering protocol work.

The current plan explicitly excludes hidden-mode activation, driver replacement, DFU and vendor-command experimentation.

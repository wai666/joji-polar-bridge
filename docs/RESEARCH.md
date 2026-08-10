# Research notes

## Device family

The current Polar Loop Gen 2 research target maps to Polar's newer TRIUMPH / `Polar_INW6F` platform, not the legacy LOOP / LOOP2 family. That distinction matters when interpreting old SDK or Flow behavior.

## PMD / real-time work

Earlier phases validated online streams for skin temperature, PPI, accelerometer and PPG under a restricted concurrency policy.

## Device-side file lifecycle

Differential experiments observed materially different lifecycle patterns across device files and SDK data:

- HR / PPI / AUTOS / sleep-intermediate data can change or roll across sync windows;
- activity sample and daily summary data persists differently;
- history/index files can update without matching the lifecycle of queue-like datasets.

These observations are used to prioritize research. They are not claims about undocumented vendor contracts.

## Current transient to investigate

A3 evidence captured a RAW inventory failure with a protobuf parse error (`invalid tag (zero)`). A later run succeeded, so the next engineering target is resilience:

- isolate inventory failure to its stage;
- preserve already committed local data;
- avoid restarting unrelated completed work;
- make retry/recovery evidence explicit;
- correlate RAW path changes over time.

## USB research branch

The PC-USB branch remains passive-first. The public research rule is to inspect enumeration/interfaces before considering any protocol work. No hidden mode activation, driver replacement, DFU, or vendor command experimentation is part of the current plan.

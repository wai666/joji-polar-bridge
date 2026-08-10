# Roadmap

**English** · [简体中文](ROADMAP_CN.md)

> [!NOTE]
> Items below are **planned work**, not claims that the features already exist or have been validated.

## V5-B1 — RAW inventory resilience + differential index

Planned:

- stage-local retry/recovery for transient inventory parse failures;
- persistent file observation metadata;
- change-frequency ranking by device path;
- size/hash/version timelines;
- correlation between RAW change events and semantic API changes;
- no expansion of device write capability.

## V5-B — longitudinal analytics

- 7 / 30 / 90-day trend windows;
- activity, sleep, HR, skin-temperature and PPI variability trends;
- personal coverage/freshness reporting;
- transparent baseline estimation.

## V5-C — explainable health intelligence

- baseline-relative alerts;
- explicit data-quality gates;
- explainable rationale rather than a black-box 0–100 score;
- derived values remain recomputable and versioned.

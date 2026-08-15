# Roadmap

**English** · [简体中文](ROADMAP_CN.md)

> [!NOTE]
> Items below are **planned work**, not claims that the features already exist or have been validated.

## P0 next decisive experiment — adjacent A/B

Design:

**A0**

- Flow disabled
- network OFF
- JOJI sync=0
- PRE_SYNC first-writer LIST
- require: `SLEEPSCO=ABSENT`

Then immediately adjacent:

**B**

- network remains OFF
- enable Flow
- foreground Flow
- no manual sync
- bounded local-only exposure

Then:

- disable Flow
- wait for ACL=N

**A1**

- JOJI first-writer LIST
- sync=0

Only A0 ABSENT → Flow-local exposure → A1 PRESENT permits the upgrade:

```text
ABSENT_TO_PRESENT_DURING_FLOW_LOCAL_EXPOSURE=CONFIRMED
NETWORK_PATH_DURING_TRANSITION=EXCLUDED
```

Even then:

```text
FLOW_LOCAL_FINAL_SCORE_CALCULATOR=UNPROVEN
```

and the design must still distinguish:

- Flow calculates/writes the score
- vs. Flow triggers device-firmware calculation.

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

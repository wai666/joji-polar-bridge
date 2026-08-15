# Research notes

**English** · [简体中文](RESEARCH_CN.md)

> [!NOTE]
> Claim labels: **Verified** = supported by recorded project evidence; **Observation** = directly observed but scope-limited; **Hypothesis** = working interpretation needing more tests; **Policy** = JOJI-imposed rule.

## P0 — SLEEPSCO first-writer research

### 5A — Research question

Primary question: which component first produces:

```text
/U/0/<date>/SLEEPSCO/SLEEPSCO.BPB
```

Candidates:

1. device firmware
2. Polar Flow local processing
3. remote/network processing

Status:

```text
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
REMOTE_FINAL_SCORE_CALCULATOR=NOT_PROVEN
DEVICE_FIRMWARE_FINAL_SCORE_CALCULATOR=NOT_PROVEN
```

### 5B — Research Agent methodology

The first-writer observer design:

- `arm-first-writer`: session flag only; no device action.
- An armed session forbids: `sync`, `reconnect-after-sync`, reset escape.
- `first-writer-connect`: exactly one attempt per session; fixed known device; no unrestricted connect API.
- Current-day GET in normal mode: `DENY`.
- First-writer mode: only the exact allowlisted current-day path `SLEEPSCO/SLEEPSCO.BPB`.
- No arbitrary current-date GET.

### 5C — Historical RAW GET milestone

Verified on device:

```text
historical SLEEPSCO.BPB: 172 bytes
phone size == PC size
phone SHA256 == PC SHA256
```

Labels:

```text
HISTORICAL_RAW_GET_DEVICE_TEST=PASS
RAW_BYTES_PRESERVED_END_TO_END=CONFIRMED
PHONE_PC_BYTE_IDENTITY=CONFIRMED
```

### 5D — Same-date PRE/POST sync

2026-08-15 controlled observation.

T0 PRE_SYNC:

- Flow/network isolation conditions
- JOJI sync=0
- current-date directory listed
- SLEEPSCO absent

Label:

```text
T0_PRE_SYNC_SLEEPSCO_ABSENT=CONFIRMED
```

Then the same-date control:

- one successful JOJI sync
- controlled reconnect
- directory listed again
- SLEEPSCO still absent

Labels:

```text
20260815_PRE_SYNC_SLEEPSCO=ABSENT
20260815_POST_SYNC_SLEEPSCO=ABSENT
SAME_DATE_PRE_POST_SYNC_DIRECTORY_SET=IDENTICAL
SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED
```

> [!NOTE]
> This does **not** claim that sync can never create SLEEPSCO.

### 5E — Overnight device-only isolation

Controlled window of approximately six hours:

- Polar Flow package disabled
- Wi-Fi OFF
- mobile data OFF
- JOJI stopped
- JOJI sync=0
- Bluetooth ON
- bond preserved

Morning PRE_SYNC observation: SLEEPSCO remained absent, while related sleep/wake data existed.

Label:

```text
SLEEPSCO_DEVICE_AUTONOMOUS_GENERATION_WITHIN_OBSERVED_WINDOW=NOT_OBSERVED
```

> [!IMPORTANT]
> This is deliberately **not** written as `DEVICE_LOCAL_GENERATION=REJECTED`: generation could be delayed or could require another trigger.

### 5F — Flow / ACL behavior

Observed:

- Flow can restart automatically after an ordinary force-stop.
- A Loop LE ACL can exist while the JOJI process is absent.
- Force-stopping an active Flow was followed by rapid ACL release in controlled observations.
- `disable-user` produced a stable Flow-isolated state.
- A foreground Flow can establish a Loop BLE ACL with the phone network OFF.

Labels:

```text
FLOW_ACL_ASSOCIATION=STRONGLY_SUPPORTED
FLOW_OWNS_ACL=UNPROVEN
FLOW_CAUSED_ACL=UNPROVEN
```

### 5G — True Flow-local offline exposure

2026-08-15:

- Polar Flow explicitly foregrounded
- Wi-Fi OFF
- mobile data OFF
- no manual Flow sync click
- JOJI sync=0
- exposure duration: approximately 11m54s

Observed throughout:

```text
FLOW_PROCESS_OBSERVED=YES
FLOW_FOREGROUND_ACTIVE=YES
FLOW_ACL_OBSERVED=YES
```

After Flow was disabled and the ACL returned to N, a JOJI first-writer connection succeeded.

PRE JOJI-sync observation:

- SLEEPSCO directory present
- SLEEPSCO.BPB present
- RAW GET exactly once: 172 bytes, phone/PC byte identity PASS

Labels:

```text
FLOW_LOCAL_OFFLINE_EXPOSURE=CONFIRMED
FLOW_CAN_ESTABLISH_LOOP_BLE_ACL_WITH_NETWORK_OFF=CONFIRMED
POST_FLOW_EXPOSURE_SLEEPSCO_PRESENT=CONFIRMED
POST_FLOW_EXPOSURE_RAW_SLEEPSCO_BPB=CONFIRMED
POST_FLOW_EXPOSURE_PRE_JOJI_SYNC=CONFIRMED

SLEEPSCO_TRANSITION_DURING_FLOW_EXPOSURE=UNPROVEN
FLOW_LOCAL_CAUSED_SLEEPSCO_APPEARANCE=UNPROVEN
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
```

### 5H — Evidence correction

Methodology correction, explicitly recorded:

- The nearest confirmed ABSENT snapshot was many hours before the Flow-local exposure.
- Therefore there was no immediately adjacent PRE-exposure snapshot.
- An earlier causal interpretation attributing SLEEPSCO creation directly to Flow-local execution was withdrawn.

## Device family

**Observation.** The current Polar Loop Gen 2 research target presents identifiers associated in this project with a newer device platform observed by JOJI rather than the legacy LOOP / LOOP2 family.

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

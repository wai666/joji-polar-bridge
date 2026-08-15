# P0 — First-Writer Research Report

**2026-08-15** · English · [简体中文](P0_FIRST_WRITER_2026-08-15_CN.md)

> [!NOTE]
> This is a sanitized public research report. Private RAW artifacts, device identifiers, session IDs and personal evidence archives are intentionally omitted.

## 1. Objective

Determine which component first produces the device-side sleep-score file:

```text
/U/0/<date>/SLEEPSCO/SLEEPSCO.BPB
```

Candidates:

1. device firmware
2. Polar Flow local processing
3. remote/network processing

## 2. Safety boundary

Throughout this research line:

```text
PFTP_WRITE=0
PFTP_DELETE=0
BOND_MUTATION=0
```

No file writes, no deletes, no pairing changes, no factory reset, no firmware operations. All device access is read-only and policy-gated. The Research Agent build does not add any device-write capability.

## 3. Research Agent architecture

The Research Agent (`0.7.0-first-writer-observer`, `versionCode=14`) adds a policy-gated observation surface to JOJI:

- PC → device command transport: ADB + `run-as` app-private inbox/outbox; no root, no network.
- Commands are session-bound, idempotent by `commandId`, and each response is archived on the PC.
- `arm-first-writer` sets a session flag only; no device action.
- An armed session forbids `sync`, `reconnect-after-sync`, and reset escape.
- `first-writer-connect` allows exactly one connect attempt per session against the fixed known device.
- Normal-mode current-day GET is denied; first-writer mode allowlists exactly one current-day path: `SLEEPSCO/SLEEPSCO.BPB`.
- Sessions end with `release`, then `export` (export is the last evidence mutation), producing a deterministic manifest.

Validated capabilities:

- session isolation
- commandId idempotency
- controlled sync
- controlled post-sync reconnect
- dynamic read-only PFTP LIST
- historical read-only PFTP GET
- RAW artifact preservation
- phone-side SHA-256, binary-safe PC pull, PC-side SHA-256
- phone/PC byte identity verification
- release → export → FINALIZED
- deterministic manifest verification

## 4. Evidence timeline

Public granularity only:

| Window | Observation |
|---|---|
| 2026-08-15 early morning | Same-date T0 PRE_SYNC control: SLEEPSCO absent; post-sync control: still absent. |
| 2026-08-15 early morning | ~6 h device-only isolation begins (Flow disabled, network OFF, JOJI stopped, bond preserved). |
| 2026-08-15 morning | Isolation ends; morning PRE_SYNC: SLEEPSCO still absent; sleep/wake data existed. |
| 2026-08-15 evening | True Flow-local offline exposure (~11m54s, network OFF, no manual sync). |
| 2026-08-15 evening | After Flow freeze and ACL=0, PRE-sync first-writer observation: SLEEPSCO present; SLEEPSCO.BPB present. |

## 5. Historical raw GET

Verified:

- historical SLEEPSCO.BPB: 172 bytes
- phone size == PC size
- phone SHA256 == PC SHA256

Labels:

```text
HISTORICAL_RAW_GET_DEVICE_TEST=PASS
RAW_BYTES_PRESERVED_END_TO_END=CONFIRMED
PHONE_PC_BYTE_IDENTITY=CONFIRMED
```

## 6. Same-date PRE/POST sync

2026-08-15 controlled observation:

- T0 PRE_SYNC under Flow/network isolation, JOJI sync=0: SLEEPSCO absent.
- Same-date control with one successful JOJI sync and controlled reconnect: SLEEPSCO still absent.

Labels:

```text
T0_PRE_SYNC_SLEEPSCO_ABSENT=CONFIRMED
20260815_PRE_SYNC_SLEEPSCO=ABSENT
20260815_POST_SYNC_SLEEPSCO=ABSENT
SAME_DATE_PRE_POST_SYNC_DIRECTORY_SET=IDENTICAL
SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED
```

> [!NOTE]
> `SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED` is scoped to this controlled observation; it does not claim sync can never create SLEEPSCO.

## 7. Overnight device-only isolation

Controlled window of approximately six hours:

- Polar Flow package disabled
- Wi-Fi OFF
- mobile data OFF
- JOJI stopped, JOJI sync=0
- Bluetooth ON
- bond preserved

Morning PRE_SYNC result: SLEEPSCO remained absent while related sleep/wake data existed.

Label:

```text
SLEEPSCO_DEVICE_AUTONOMOUS_GENERATION_WITHIN_OBSERVED_WINDOW=NOT_OBSERVED
```

> [!IMPORTANT]
> Deliberately not written as `DEVICE_LOCAL_GENERATION=REJECTED` — generation could be delayed or could require another trigger.

## 8. Flow/ACL observations

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

## 9. Flow-local offline exposure

2026-08-15, evening:

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

After Flow was disabled and the ACL returned to N, a JOJI first-writer connection succeeded. PRE JOJI-sync observation:

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

## 10. Evidence correction

Explicitly recorded methodology correction:

- The nearest confirmed ABSENT snapshot was many hours before the Flow-local exposure.
- There was therefore no immediately adjacent PRE-exposure snapshot.
- An earlier causal interpretation attributing SLEEPSCO creation directly to Flow-local execution was withdrawn.

This correction is preserved as part of the P0 record.

## 11. Current conclusion

```text
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
SLEEPSCO_TRANSITION_DURING_FLOW_EXPOSURE=UNPROVEN
FLOW_LOCAL_CAUSED_SLEEPSCO_APPEARANCE=UNPROVEN
```

The P0 computation / first-writer locus remains unresolved.

The current evidence disfavors simplistic hypotheses such as “JOJI sync immediately creates SLEEPSCO” — but it does **not** reject device firmware, Flow-local processing, or a remote/network path; none of these is excluded by the evidence recorded so far.

## 12. Next decisive experiment

Adjacent A/B design (see [`ROADMAP.md`](ROADMAP.md)):

- **A0**: Flow disabled, network OFF, JOJI sync=0, PRE_SYNC first-writer LIST — require `SLEEPSCO=ABSENT`.
- **B**: network stays OFF; enable and foreground Flow; no manual sync; bounded local-only exposure; then disable Flow and wait for ACL=N.
- **A1**: JOJI first-writer LIST, sync=0.

Only A0 ABSENT → Flow-local exposure → A1 PRESENT permits the upgrade:

```text
ABSENT_TO_PRESENT_DURING_FLOW_LOCAL_EXPOSURE=CONFIRMED
NETWORK_PATH_DURING_TRANSITION=EXCLUDED
```

Even then, `FLOW_LOCAL_FINAL_SCORE_CALCULATOR=UNPROVEN` — the design must still distinguish whether Flow calculates/writes the score itself, or triggers device-firmware calculation.

---

Related: [`RESEARCH.md`](RESEARCH.md) · [`BUILD_STATUS.md`](BUILD_STATUS.md)

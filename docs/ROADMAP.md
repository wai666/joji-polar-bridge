# Roadmap

**English** · [简体中文](ROADMAP_CN.md)

> [!NOTE]
> Completed items below are explicitly marked **Verified**. Unmarked future items remain planned work.

## V5-B1 — historical coverage protection + restart recovery

**Verified 2026-08-10:**

- persisted HR semantic versions are aggregated by stable group key;
- richer historical payloads prevent sparse latest-payload coverage regression;
- contributing payload SHA-256 values form the selected source fingerprint;
- stale local `RUNNING` sessions recover as `INTERRUPTED_RESTART`;
- direct Java 21 compile/test/lint/assemble validation is PASS.

## USB research track — R5I

**Verified 2026-08-10:**

- software-gated Windows USB enumeration;
- `VID_0DA4:PID_0014` CDC ACM interface bound to `usbser`;
- Polar PFTP real-device read-only `GET /DEVICE.BPB` over USB;
- bounded single-level directory listings for `/`, `/U/`, `/U/0/`, `/U/0/<date>/` (earlier window);
- R5E + R5F: valid PFTP error 103 (NO_SUCH_FILE_OR_DIRECTORY) on an earlier date path and child, transport validated;
- R5G: `/U/0/` re-listing confirmed date index membership changed — earlier date absent, later date present, non-date entries unchanged;
- R5I: current date-directory listing returned `ACT/`, `DSUM/`, `SKINCONT/` — entry-name set differs from earlier completed-day sample (`SKINCONT/`, `SKINTEMP/`);
- no firmware operation and no device-file mutation.

Detailed evidence: [`../research/usb/USB_EVIDENCE_20260810.md`](../research/usb/USB_EVIDENCE_20260810.md)

Next USB work remains fixed-path, non-recursive and read-only unless a new safety gate is reviewed.

## V5-B — longitudinal analytics

Planned:

- 7 / 30 / 90-day trend windows;
- activity, sleep, HR, skin-temperature and PPI variability trends;
- personal coverage/freshness reporting;
- transparent baseline estimation.

## V5-C — explainable health intelligence

Planned:

- baseline-relative alerts;
- explicit data-quality gates;
- explainable rationale rather than a black-box 0–100 score;
- derived values remain recomputable and versioned.

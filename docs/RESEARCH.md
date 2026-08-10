# Research notes

**English** · [简体中文](RESEARCH_CN.md)

> [!NOTE]
> Claim labels: **Verified** = supported by recorded project evidence; **Observation** = directly observed but scope-limited; **Hypothesis** = working interpretation needing more tests; **Policy** = JOJI-imposed rule.

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

## RAW inventory transient

**Observation.** A3 evidence captured a RAW inventory failure with protobuf parse error `invalid tag (zero)`. A later run succeeded.

**Policy.** Local processing and recovery should isolate this class of failure to its stage, preserve already committed data, and keep retry/recovery evidence explicit.

## USB research branch

**Verified — tested device and recorded-session scope.** The SDK-exposed USB connection setting acts as a software gate for Windows host enumeration. With the setting enabled, Windows enumerated `VID_0DA4:PID_0014` as a composite USB device with one observed CDC ACM interface bound to the built-in `usbser` driver. Restoring the setting to OFF removed that enumeration.

**Verified.** A fixed read-only Polar PFTP `GET /DEVICE.BPB` request succeeded over the CDC ACM serial transport at 115200 8N1 with RTS/CTS. The returned protobuf matched the tested Loop Gen 2 model and firmware family. Public documentation omits the personal device ID and other user-specific identifiers.

**Verified — bounded filesystem mapping.** Directory-only PFTP reads succeeded for `/`, `/U/`, `/U/0/`, and `/U/0/<date>/`. The `/U/0/` listing included date-shaped directories plus `AUTOS/`, `DGOAL/`, `NR/`, `SLEEP/`, `SPROF/`, `S/`, `TL/` and user-related metadata entries. A single daily directory listing confirmed `SKINCONT/` and `SKINTEMP/` subdirectories. Public documentation intentionally does not publish user-specific date names or identifiers.

**Observation.** The Windows USB bus descriptor product string and the internal `DEVICE.BPB` model name are different identifiers. JOJI treats them as separate descriptor/model layers rather than assuming they are interchangeable.

**Policy.** USB work remains fixed-path and read-only by default. No directory recursion, arbitrary file reads, driver replacement, DFU, firmware write, raw vendor-command exploration or device-file mutation is allowed without a new reviewed gate. The only device-side setting mutation exercised in this branch is the dedicated reversible USB connection-mode toggle, with pre-read, immediate readback and OFF restoration.

Detailed evidence: [`../research/usb/USB_EVIDENCE_20260810.md`](../research/usb/USB_EVIDENCE_20260810.md)

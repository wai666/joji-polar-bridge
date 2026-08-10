<!-- JOJI Polar Bridge V5-B1 / USB-R5G — updated 2026-08-11 -->
<div align="center">
  <img src="assets/hero.svg" alt="JOJI Polar Bridge" width="100%" />

  <br/>

  **Turn Polar Loop Gen 2 device data into verifiable, traceable, durable local health data.**

  *A privacy-first local health data bridge and research platform for Polar Loop Gen 2.*

  **English** · [简体中文](README_CN.md)

  ![Platform](https://img.shields.io/badge/platform-Android-3DDC84?logo=android&logoColor=white)
  ![SDK](https://img.shields.io/badge/Polar%20BLE%20SDK-8.1.0-00A6CE)
  ![Database](https://img.shields.io/badge/storage-SQLite%20schema%20v2-0f80cc)
  ![Derived](https://img.shields.io/badge/derived-A3.D2-7c5cff)
  ![USB](https://img.shields.io/badge/USB-CDC%20ACM%20%2B%20PFTP-00A6CE)
  ![Safety](https://img.shields.io/badge/device%20writes-reviewed%20USB%20gate-f0ad4e)
  ![Status](https://img.shields.io/badge/V5--B1-verified-2ea44f)
</div>

---

## What is JOJI?

**JOJI Polar Bridge** is an Android local data bridge and research platform for **Polar Loop Gen 2**. The goal is not to build another sync screen, but a durable data layer that can evolve without losing provenance:

- read supported activity, heart-rate, PPI, sleep, Nightly Recharge, skin-temperature and related data through **Polar BLE SDK 8.1.0**;
- archive reviewed read-only device artifacts with SHA-256 content identities;
- keep original SDK semantics, RAW artifacts and JOJI-derived metrics in separate layers;
- distinguish genuinely changed data from exact repeats during incremental sync;
- retain algorithm versions and source fingerprints so derived history can be recomputed and compared;
- preserve evidence for connection behavior, permission boundaries and recovery paths instead of hiding failures.

> [!IMPORTANT]
> This repository is an independent research project and is **not affiliated with or endorsed by Polar Electro**.

## Claim labels

Technical statements use explicit scope labels:

| Label | Meaning |
|---|---|
| **Verified** | Supported by this project's recorded build, test or real-device evidence for the stated scope. |
| **Observation** | Directly observed during experiments, but not treated as a vendor guarantee or universal behavior. |
| **Hypothesis** | A working interpretation that still requires additional controlled testing. |
| **Policy** | A JOJI-imposed engineering or safety rule; not a claim about Polar's requirements. |

See [`docs/RESEARCH.md`](docs/RESEARCH.md) for the research-specific annotations.

## Current milestone — V5-B1 / A3.D2

**Verified — project validation scope:** V5-B1 / A3.D2 is the current verified host-build and offline-processing milestone.

| Area | Status | Scope / notes |
|---|---:|---|
| Windows Android build | ✅ PASS | Kotlin compile, unit tests, lint, assemble, safety/privacy gates |
| Local persistence | ✅ PASS | SQLite + app-private RAW archive |
| Schema migration | ✅ PASS | explicit `v1 → v2` migration |
| Incremental sync / dedup | ✅ PASS | logical key + payload SHA-256 |
| Derived versioning | ✅ PASS | A3.D2 source aggregation + algorithm version + source fingerprint |
| Historical HR coverage protection | ✅ PASS | richer persisted HR groups prevent sparse latest-payload regression |
| Restart recovery | ✅ PASS | stale local `RUNNING` sessions become `INTERRUPTED_RESTART` |
| Manual recompute idempotency | ✅ PASS | unchanged inputs deduplicate instead of creating new rows |
| Safe release | ✅ PASS | successful validated sessions end with callback-confirmed disconnect |
| USB software gate | ✅ PASS | controlled ON/OFF setting experiment with immediate readback and OFF restore |
| USB transport | ✅ PASS | Windows CDC ACM / `usbser` + real-device PFTP read-only GET verified |
| Device-side mutation scope | restricted | only the reviewed reversible USB connection-mode gate has been exercised; file/firmware mutation remains forbidden |
| Known transient | ⚠️ tracked | RAW inventory protobuf parsing has failed transiently in recorded evidence |

Latest sealed APK SHA-256 recorded by the project:

```text
baa9e3c42a7bc6fbcadcf5af92755d75443e3b2f35774e915be4c7685a264b97
```

Detailed validation status: [`docs/BUILD_STATUS.md`](docs/BUILD_STATUS.md)

## Architecture

```mermaid
flowchart LR
    Loop[Polar Loop Gen 2] -->|BLE / SDK| SDK[Polar BLE SDK 8.1.0]
    SDK --> RAW[L0 · Evidence\nRAW + original SDK payload]
    RAW --> CAN[L1 · Canonical\nsource priority + freshness]
    CAN --> DER[L2 · Derived\nA3.D2 versioned metrics]
    DER --> UI[L3 · Presentation\ndashboard · history · trends]

    RAW --> ARCH[(Content-addressed archive)]
    CAN --> DB[(SQLite)]
    DER --> DB

    style Loop fill:#152238,stroke:#54dff7,color:#fff
    style RAW fill:#172337,stroke:#ffb86b,color:#fff
    style CAN fill:#172d35,stroke:#4de2c2,color:#fff
    style DER fill:#211f3b,stroke:#9c82ff,color:#fff
    style UI fill:#183226,stroke:#63e58e,color:#fff
```

### Four-layer data model

1. **L0 Evidence** — immutable RAW artifacts and original SDK payloads.
2. **L1 Canonical** — normalized records with explicit source authority and freshness.
3. **L2 Derived** — transparent, recomputable JOJI metrics that never overwrite source data.
4. **L3 Presentation** — dashboard, health views, history and trends.

**Design rule:** derived values are never treated as raw truth.

More detail: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md)

## Real-device validation highlights

### Verified — USB software gate and transport

Controlled real-device testing established a reversible relationship between the SDK-exposed USB connection setting and Windows enumeration:

```text
USB mode ON  -> VID_0DA4:PID_0014 enumerates and exposes a CDC ACM serial interface
USB mode OFF -> the device no longer enumerates on the Windows USB host
```

Windows bound the interface to the built-in `usbser` driver. A single fixed read-only PFTP `GET /DEVICE.BPB` request over the CDC ACM transport succeeded and returned a valid Polar device-info protobuf matching the tested Loop Gen 2 model and firmware family.

Subsequent read-only directory listings verified the PFTP filesystem path hierarchy through `/`, `/U/`, and `/U/0/` without recursive enumeration or device-file mutation. Public documentation intentionally omits personal device identifiers and user-specific date paths.

### Verified — recompute idempotency

Within the recorded A3 real-device validation set, an initial `A3.D1` recompute created four derived records. Repeating manual recompute against unchanged source data produced:

```text
new=0
duplicate=4
```

### Verified — changed source creates a new derived version

After a later successful sync changed part of the underlying dataset, the recorded A3.D1 recompute produced:

```text
new=1
duplicate=3
```

For this implementation and validation set, the intended rule holds: **change creates history; no-change stays deduplicated.**

## Supported semantic domains

Current project work includes:

- Steps
- Activity samples
- Daily summary
- Active time
- Distance
- Calories
- 24/7 heart rate
- 24/7 PPI
- Sleep
- Nightly Recharge
- Skin temperature
- Training references / sessions (an empty result is treated as a valid current state)
- SpO₂ tests (an empty result is treated as a valid current state)

## Safety model

**Policy:** JOJI intentionally keeps a narrow device-side safety boundary.

**Allowed by project policy**

- reuse the existing Android BLE bond;
- read supported SDK health/history APIs;
- start/stop approved online PMD streams;
- perform reviewed read-only file retrieval through the existing whitelist;
- perform narrowly reviewed USB PFTP read-only requests on fixed paths during a controlled research gate;
- toggle the SDK USB connection-mode setting only inside an explicit reversible experiment with pre-read, post-readback and OFF restore;
- archive and process retrieved data locally.

**Forbidden by project policy**

- executable use of SDK `readFile`;
- file write/delete operations;
- log-configuration mutation;
- bond creation/removal;
- factory reset or firmware operations;
- SDK-mode activation;
- offline-recording mutation;
- ECG start;
- arbitrary persistent device mutation outside the reviewed USB connection-mode gate.

See [`docs/SAFETY.md`](docs/SAFETY.md).

## Research findings

**Observation:** differential experiments indicate that device-side datasets do not all follow the same lifecycle. In the current evidence set, some HR/PPI/AUTOS/sleep-intermediate artifacts changed or rolled across sync windows while activity summaries and history/index artifacts behaved differently.

**Hypothesis:** some of these datasets may behave like rolling or consumable sync queues. This remains a research interpretation, **not an undocumented vendor guarantee**.

See [`docs/RESEARCH.md`](docs/RESEARCH.md).

### Verified USB research findings

The current tested device exposes a software-gated USB data path. When enabled, Windows observes `VID_0DA4:PID_0014` as a composite device with one CDC ACM interface bound to `usbser`. Real-device testing verified Polar PFTP framing over that serial transport and a fixed read-only `GET /DEVICE.BPB` transaction. Directory-only reads then mapped the root, `/U/`, and `/U/0/` one level at a time.

The exact USB bus descriptor product string and the `DEVICE.BPB` model name are different identifiers and are documented as separate layers rather than treated as interchangeable product names.

Detailed evidence: [`research/usb/USB_EVIDENCE_20260810.md`](research/usb/USB_EVIDENCE_20260810.md)

## Roadmap

```text
V5-A3   ✅ Versioned derived layer + local research index
  ↓
V5-B1   ✅ A3.D2 historical coverage protection + restart recovery + verified build
  ↓
USB-R5D ✅ Software gate + CDC ACM + PFTP + bounded read-only directory mapping including daily directory
  ↓
V5-B    ○ 7 / 30 / 90-day analytics + personal baseline
  ↓
V5-C    ○ explainable baseline alerts / health intelligence
```

Next focus: continue bounded, read-only evidence collection where it directly improves semantic understanding, while progressing V5-B longitudinal analytics. USB work remains fixed-path, non-recursive and mutation-restricted.

See [`docs/ROADMAP.md`](docs/ROADMAP.md).

## Privacy

The public repository intentionally excludes:

- personal health export JSON;
- full live evidence logs;
- Bluetooth MAC addresses and device IDs;
- private handoff archives;
- app-private RAW payloads;
- user-specific sync history.

Public documentation contains sanitized engineering facts and aggregated validation results only.

## Repository status

This is an actively developed personal research project. The public repository currently publishes the **architecture, validation model, safety policy and research roadmap**. Application source code is not part of this public package at this stage.

---

<div align="center">
  <sub>Built around evidence, reproducibility, explicit claim scope, and a strict no-device-mutation boundary.</sub>
</div>

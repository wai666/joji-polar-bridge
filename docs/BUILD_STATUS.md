# Build & validation status

## Research Agent 0.7.0 — 2026-08-15

Research build:

```text
versionName=0.7.0-first-writer-observer
versionCode=14
APK SHA-256: 1c9a2a77adcfb91db8b56ff83d01043449df29f5be510e12c1dc9335143ed88a
```

Build/test status:

- JDK 21 Android build PASS
- 301 unit tests PASS
- static safety scan PASS
- USB-R3 safety scan PASS
- PC CLI self-test PASS

Evidence integrity:

- sessionId binding
- cross-session contamination protection
- commandId idempotency
- RELEASED → FINALIZED lifecycle
- post-finalization mutation denied
- deterministic manifest

Real-device validation:

```text
Historical Raw GET:             PASS
RAW_BYTES_PRESERVED_END_TO_END: CONFIRMED
PHONE_PC_BYTE_IDENTITY:         CONFIRMED
```

Known tested SLEEPSCO.BPB size: 172 bytes

## V5-A3 R3

Windows runner:

Validation run: 2026-08-10

Result: **PASS**

Validated gates:

- Python runner syntax
- baseline preflight
- source patch proof
- DB schema self-test
- explicit schema 1→2 migration self-test
- A3 processing self-test
- Polar dependency proof
- Kotlin compilation
- unit tests
- Android lint
- APK assemble
- static safety scan
- privacy scan
- manifest audit
- fixture verification

APK SHA-256:

```text
b978ed1ceade315586b6c298eefa66b2912d677292af47021dcc3d33141672c7
```

The public project intentionally does not publish the private APK or personal evidence archive.

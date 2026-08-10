# Build & validation status

## Phase I — Final Candidate (2026-08-11)

Result: **PASS**

### Changes
- 8 evidence-supported implementation items (Phase H audit)
- Passive USB PFTP evidence model
- Device metadata (DEVICE.BPB fields)
- SKIN_CONTACT HistoricalKind
- Platform identity documentation (INW5T vs INW6F)
- Date lifecycle awareness annotations
- Safety policy USB gate documentation

### Validated gates

- Kotlin compilation;
- Java compilation (JDK 21);
- unit tests;
- Android lint;
- APK assemble;
- frozen source unchanged;
- privacy scan (0 new private identifiers);
- static safety scan (0 new executable device operations);
- proprietary-code audit.

### Phase I APK SHA-256

```text
8b9c1c75e4a561364846cc3eaba224ccd8c5574d6ca910d5aec2f2b552f8798c
```

### Prior B1 APK SHA-256 (for reference)

```text
baa9e3c42a7bc6fbcadcf5af92755d75443e3b2f35774e915be4c7685a264b97
```

### Previous builds

#### V5-B1 / A3.D2 (2026-08-10)
All gates PASS. Snapshot: 49 files, SHA-256: 29213509...

The public project intentionally does not publish the private APK, device identifiers, personal health payloads or full evidence archive.

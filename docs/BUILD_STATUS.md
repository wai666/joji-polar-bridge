# Build & validation status

## V5-B1 / A3.D2

Validation date: 2026-08-10

Result: **PASS**

### Source snapshot gate

- source snapshot created before B1 implementation;
- source-unchanged proof: **PASS**;
- snapshot file count: **49**;
- snapshot SHA-256:

```text
292135092723b33d4f9e84b6f9899f792b502868e34de46d455b4b2901776d9a
```

### B1 implementation scope

- aggregate persisted 24/7 HR semantic versions by stable group key;
- select the richer payload within a group, with newer data as tie-breaker;
- derive source fingerprints only from contributing payload SHA-256 values;
- protect historical coverage from regression when a newer payload is sparse;
- convert stale `RUNNING` sessions to `INTERRUPTED_RESTART` locally;
- keep PPI aggregation behavior intact.

Offline replay evidence for the recorded dataset increased selected HR coverage from a sparse latest payload to the richer persisted history without modifying device data.

### Direct Java 21 validation

Validated gates:

- source preflight;
- Java 21 runtime and compiler;
- Gradle 8.12;
- Kotlin compilation;
- Java compilation;
- unit tests;
- Android lint;
- APK assemble;
- source post-build proof.

```text
SOURCE_PREFLIGHT=PASS
JAVA21=PASS
JAVAC21=PASS
GRADLE_VERSION=PASS
KOTLIN_COMPILE=PASS
JAVA_COMPILE=PASS
UNIT_TEST=PASS
LINT=PASS
ASSEMBLE=PASS
SOURCE_POSTBUILD=PASS
RESULT=PASS
DEVICE_CONTACTED=NO
```

Latest verified APK SHA-256:

```text
baa9e3c42a7bc6fbcadcf5af92755d75443e3b2f35774e915be4c7685a264b97
```

The public project intentionally does not publish the private APK, device identifiers, personal health payloads or full evidence archive.

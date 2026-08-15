# Changelog

## Research Agent 0.7.0 / P0 First-Writer — 2026-08-15

- Research Agent architecture introduced.
- Controlled post-sync reconnect.
- Evidence-integrity lifecycle.
- Historical raw GET verified.
- Byte-preserving PC export verified.
- First-writer observer introduced.
- PRE/POST sync SLEEPSCO control.
- Overnight device-only observation.
- Flow-local offline exposure.
- Causal interpretation corrected due missing adjacent PRE snapshot.

## V5-A3 — 2026-08-10

- SQLite schema v2 with explicit v1→v2 migration.
- Versioned daily derived metrics.
- Explicit `A3.D1` algorithm identifier.
- Source fingerprints for recomputation/dedup.
- Source-consistency visibility between dedicated APIs and daily summary snapshots.
- Local RAW change-hotspot research index.
- Manual local recompute without connecting to the Loop.
- Real-device validation of recompute idempotency and source-change versioning.

## V5-A2

- Canonical source authority and freshness model.
- Daily health presentation and 7-day trend surfaces.
- Dedicated APIs remain authoritative for current steps/distance/calories/activity time.

## V5-A1

- Local SQLite persistence.
- Content-addressed RAW archive.
- Incremental sync checkpoints.
- Semantic and RAW deduplication.
- First real-device two-sync acceptance proving incremental behavior.

## V4.3 / R4

- Differential T0/T1/T2 research workflow.
- Safe release and evidence packaging.
- Advertisement-gated bounded connection recovery.

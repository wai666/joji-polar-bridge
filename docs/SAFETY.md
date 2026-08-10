# Safety boundary

**English** · [简体中文](SAFETY_CN.md)

> [!IMPORTANT]
> **Policy:** the rules below are JOJI-imposed safety constraints for this research project. They are not presented as Polar requirements.

The project treats the existing Polar Loop as a live personal device, not a disposable test target.

## Invariants

- Reuse the existing Android bond.
- Do not create or remove bonds.
- Do not reset or factory-reset the device.
- Do not initiate firmware operations.
- Do not enable SDK mode.
- Do not mutate offline recording state.
- Do not start ECG.
- Do not execute SDK `readFile`.
- Do not call device file write/delete APIs.
- Do not change device log configuration.
- Do not send manual/raw device-file mutation commands.
- Preserve the sealed V4.3/R4 connection recovery state machine unless a demonstrated defect requires a reviewed change.

## Reviewed USB exception

The project no longer claims that zero device-setting writes have ever occurred. A dedicated USB research gate exercised the SDK USB connection-mode setting as a **reversible, bounded exception**:

- read the current value first;
- write only when the requested state differs from the observed baseline;
- immediately read back the value;
- restore USB mode to OFF after the experiment;
- never combine this gate with firmware, file-write/delete, reset, bond or log-configuration mutation.

This exception does **not** authorize arbitrary persistent device mutation.

## File access

Executable BLE-side file retrieval remains constrained to previously reviewed fixed paths / whitelists. The public repository does not publish private device payloads.

For the USB research branch, separately reviewed fixed-path PFTP `GET` requests may be used for read-only protocol validation. Directory mapping is single-level and non-recursive unless a later gate explicitly expands that scope. User identifiers and personal payloads remain excluded from public artifacts.

## Release behavior

Successful BLE sessions are expected to terminate with callback-confirmed disconnect. A permission denial on protected device paths is treated as a boundary signal, not something to bypass. USB connection mode is restored to OFF after a completed research window unless an explicitly documented next read-only gate is being executed in the same bounded session.

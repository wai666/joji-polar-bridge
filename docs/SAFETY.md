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

## File access

Executable file retrieval is constrained to the previously reviewed V4.3 whitelist. The public repository does not publish private device payloads.

## Release behavior

Successful sessions are expected to terminate with callback-confirmed disconnect. A permission denial on protected device paths is treated as a boundary signal, not something to bypass.

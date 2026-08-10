# USB Research Evidence — 2026-08-10

**English**

> [!NOTE]
> This document records sanitized USB research evidence. Personal device identifiers, user-specific date paths, and private payloads are intentionally excluded.

## Gates executed

| Gate | Path | Type | Result | Bytes | Entries |
|---|---|---|---|---|---|
| R4B | — | FlowSync offline scan | STOP | — | FlowSync not installed |
| R4C | `/DEVICE.BPB` | PFTP GET file | PASS | 219 | 1 file |
| R4D | — | Offline protobuf decode | PASS | — | PbDeviceInfo matched |
| R5A | `/` | PFTP GET directory | PASS | 247 | 16 entries |
| R5B | `/U/` | PFTP GET directory | PASS | 21 | 2 entries |
| R5C | `/U/0/` | PFTP GET directory | PASS | 183 | 14 entries |
| R5D | `/U/0/<date>/` | PFTP GET directory | PASS | 30 | 2 entries |

## Transport verified

- **USB enumeration:** `VID_0DA4:PID_0014` composite device, CDC ACM interface bound to Windows `usbser`
- **Serial:** 115200 8N1, RTS/CTS flow control
- **Upper layer:** Polar PFTP framing over CDC ACM
- **Operations:** read-only `GET` (command 0x00), fixed single paths only

## Filesystem mapping (read-only, single-level)

### `/` (root)
16 entries including `DATACOLL/`, `ERRORLOG.BPB`, `NR/`, `OFF_TRIG.BIN`, `REST/`, `SDLOGS.BPB`, `SDLOGS/`, `SKIN_TEMP_BL.BIN`, `SYSCONF.BPB`, `SYSLOG.BPB`, `SYS/`, `U/`, `PRODDATA.BIN`, `DEVICE.BPB`, `SYSLOG.TXT`, `PMDFILES.TXT`

### `/U/`
2 entries: `0/` (directory), `UDB.BPB` (file)

### `/U/0/`
14 entries: date-shaped directories, `AUTOS/`, `DGOAL/`, `NR/`, `SLEEP/`, `SLPRRSTD.BIN`, `SPROF/`, `S/`, `TL/`, `USERID.BPB`

### `/U/0/<date>/`
2 entries: `SKINCONT/`, `SKINTEMP/`

## Safety invariants maintained

- Zero directory recursion
- Zero child file content reads
- Zero filesystem mutation (PUT/MERGE/REMOVE)
- Zero firmware operations
- USB mode restored to OFF after each research window
- Independent readback probe confirms OFF baseline each time
- Reviewed reversible USB connection-mode gate is the only device-setting mutation exercised

## What was NOT done

- No arbitrary path exploration
- No file content retrieval beyond `/DEVICE.BPB`
- No driver replacement
- No DFU
- No raw vendor commands
- No directory recursion

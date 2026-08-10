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
| R5E | `/U/0/<date>/SKINTEMP/` | PFTP GET directory | STOP | — | error 103: NO_SUCH_FILE_OR_DIRECTORY |
| R5F | `/U/0/<date>/` | PFTP GET directory | STOP | — | error 103: NO_SUCH_FILE_OR_DIRECTORY |
| R5G | `/U/0/` | PFTP GET directory | PASS | 183 | 14 entries |
| R5I | `/U/0/<current-date>/` | PFTP GET directory | PASS | 36 | 3 entries |

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
In an earlier research window: 2 entries — `SKINCONT/`, `SKINTEMP/`. In a later independent window, the same date path returned PFTP error 103 (NO_SUCH_FILE_OR_DIRECTORY), and a subsequent `/U/0/` re-listing showed that the earlier date entry had left the directory index while a later date entry had appeared. Non-date entries remained unchanged across observations.

## Date-shaped directory lifecycle (unresolved)

**R5E (STOP):** a direct GET of `/U/0/<earlier-date>/SKINTEMP/` returned PFTP error 103. Transport and request framing were validated; exactly one GET was sent.

**R5F (STOP):** the parent path `/U/0/<earlier-date>/` also returned PFTP error 103. No parent listing was obtained.

**R5G (PASS):** a re-listing of `/U/0/` confirmed that the earlier date entry was absent while a later date entry was present. All non-date entries were unchanged.

**R5I (PASS):** a single-level listing of one currently indexed date-shaped directory returned 3 entries: `ACT/`, `DSUM/`, `SKINCONT/`. An earlier completed-day listing (R5D) had returned `SKINCONT/`, `SKINTEMP/`. The entry-name sets differ between the observed current-day and completed-day samples. `SKINCONT/` was present in both. The structural variation is observed without claiming a universal schema or lifecycle mechanism.

**Status:** the date-shaped directory index under `/U/0/` exhibits dynamic membership. Individual date directories show single-level structural variation between observed samples. The retention/lifecycle mechanism remains unresolved and no specific mechanism (rotation, consumption, cleanup, FIFO) is claimed as confirmed.

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

# USB 研究证据 — 2026-08-10

**简体中文**

> [!NOTE]
> 本文记录脱敏后的 USB 研究证据。个人设备标识、用户专属日期路径和私有 payload 已排除。

## 已执行 Gate

| Gate | 路径 | 类型 | 结果 | 字节数 | 条目数 |
|---|---|---|---|---|---|
| R4B | — | FlowSync 离线扫描 | STOP | — | FlowSync 未安装 |
| R4C | `/DEVICE.BPB` | PFTP GET 文件 | PASS | 219 | 1 文件 |
| R4D | — | 离线 protobuf 解码 | PASS | — | PbDeviceInfo 匹配 |
| R5A | `/` | PFTP GET 目录 | PASS | 247 | 16 条目 |
| R5B | `/U/` | PFTP GET 目录 | PASS | 21 | 2 条目 |
| R5C | `/U/0/` | PFTP GET 目录 | PASS | 183 | 14 条目 |
| R5D | `/U/0/<日期>/` | PFTP GET 目录 | PASS | 30 | 2 条目 |

## 已验证的 Transport

- **USB 枚举:** `VID_0DA4:PID_0014` composite device，CDC ACM 接口绑定 Windows `usbser`
- **串口参数:** 115200 8N1, RTS/CTS
- **上层协议:** Polar PFTP framing over CDC ACM
- **操作类型:** 只读 `GET` (command 0x00)，仅固定单路径

## 文件系统映射（只读、单层）

### `/`（根目录）
16 条目，含 `DATACOLL/`、`ERRORLOG.BPB`、`NR/`、`OFF_TRIG.BIN`、`REST/`、`SDLOGS.BPB`、`SDLOGS/`、`SKIN_TEMP_BL.BIN`、`SYSCONF.BPB`、`SYSLOG.BPB`、`SYS/`、`U/`、`PRODDATA.BIN`、`DEVICE.BPB`、`SYSLOG.TXT`、`PMDFILES.TXT`

### `/U/`
2 条目：`0/`（目录）、`UDB.BPB`（文件）

### `/U/0/`
14 条目：日期目录、`AUTOS/`、`DGOAL/`、`NR/`、`SLEEP/`、`SLPRRSTD.BIN`、`SPROF/`、`S/`、`TL/`、`USERID.BPB`

### `/U/0/<日期>/`
2 条目：`SKINCONT/`、`SKINTEMP/`

## 维持的安全不变量

- 零目录递归
- 零子文件内容读取
- 零文件系统修改（PUT/MERGE/REMOVE）
- 零固件操作
- 每次 research window 后 USB mode 恢复 OFF
- 独立 readback 探针每次确认 OFF baseline
- 唯一执行过的 device-setting mutation = 经审核、可逆的 USB connection-mode gate

## 未执行

- 无任意路径探索
- 无 `/DEVICE.BPB` 以外的文件内容读取
- 无 driver replacement
- 无 DFU
- 无 raw vendor command
- 无目录递归

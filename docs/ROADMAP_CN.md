# 路线图

[English](ROADMAP.md) · **简体中文**

> [!NOTE]
> 已完成项目会明确标记 **Verified / 已验证**；未标记的后续内容仍属于计划工作。

## V5-B1 — 历史 coverage 保护 + restart recovery

**2026-08-10 已验证：**

- persisted HR semantic versions 按 stable group key 聚合；
- richer historical payload 防止 sparse latest payload 导致 coverage regression；
- 仅 contributing payload SHA-256 参与 selected source fingerprint；
- stale local `RUNNING` session 恢复为 `INTERRUPTED_RESTART`；
- Java 21 direct compile/test/lint/assemble validation PASS。

## USB 研究线 — R5I

**2026-08-10 已验证：**

- software-gated Windows USB enumeration；
- `VID_0DA4:PID_0014` CDC ACM interface 绑定 `usbser`；
- Polar PFTP 通过 USB 完成实机只读 `GET /DEVICE.BPB`；
- `/`、`/U/`、`/U/0/`、`/U/0/<日期>/` 完成受控 single-level directory listing（早期 window）；
- R5E + R5F：在较早日期路径及子目录上返回有效 PFTP error 103（NO_SUCH_FILE_OR_DIRECTORY），transport 已验证；
- R5G：`/U/0/` 重新 listing 确认日期索引成员已变——较早日期已不在，较晚日期已出现，非日期条目未变；
- R5I：当前日期目录 listing 返回 `ACT/`、`DSUM/`、`SKINCONT/`——entry-name set 与更早的已完成日样本（`SKINCONT/`、`SKINTEMP/`）不同；
- 无 firmware operation、无 device-file mutation。

详细证据：[`../research/usb/USB_EVIDENCE_20260810_CN.md`](../research/usb/USB_EVIDENCE_20260810_CN.md)

下一步 USB 工作继续保持 fixed-path、non-recursive、read-only，除非有新的 safety gate 明确批准扩展。

## V5-B — 长期趋势分析

计划：

- 7 / 30 / 90 天趋势窗口；
- activity、sleep、HR、skin-temperature、PPI variability 趋势；
- 个人 coverage/freshness 报告；
- 透明的 baseline estimation。

## V5-C — 可解释健康智能

计划：

- 相对个人 baseline 的告警；
- 明确的数据质量 gate；
- 给出可解释原因，而不是黑盒 0–100 分；
- Derived 值继续保持可重算和版本化。

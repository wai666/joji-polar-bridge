# 路线图

[English](ROADMAP.md) · **简体中文**

> [!NOTE]
> 以下内容属于**计划工作**，不表示功能已经完成或已经验证。

## V5-B1 — RAW inventory 韧性 + 差分索引

计划：

- 对瞬态 inventory parse failure 做阶段局部 retry/recovery；
- 持久保存 file observation metadata；
- 按 device path 统计变化频率；
- 建立 size/hash/version timeline；
- 关联 RAW change event 与 semantic API change；
- 不扩大设备写入能力。

## V5-B — 长期趋势分析

- 7 / 30 / 90 天趋势窗口；
- activity、sleep、HR、skin-temperature、PPI variability 趋势；
- 个人 coverage/freshness 报告；
- 透明的 baseline estimation。

## V5-C — 可解释健康智能

- 相对个人 baseline 的告警；
- 明确的数据质量 gate；
- 给出可解释原因，而不是黑盒 0–100 分；
- Derived 值继续保持可重算和版本化。

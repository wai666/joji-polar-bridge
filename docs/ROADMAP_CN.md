# 路线图

[English](ROADMAP.md) · **简体中文**

> [!NOTE]
> 以下内容属于**计划工作**，不表示功能已经完成或已经验证。

## P0 下一步决定性实验 — 紧邻 A/B

设计：

**A0**

- Flow 禁用
- 网络 OFF
- JOJI sync=0
- PRE_SYNC first-writer LIST
- 要求：`SLEEPSCO=ABSENT`

随后紧邻：

**B**

- 网络保持 OFF
- 启用 Flow
- Flow 置于前台
- 无手动同步
- 有界 local-only 暴露

随后：

- 禁用 Flow
- 等待 ACL=N

**A1**

- JOJI first-writer LIST
- sync=0

只有 A0 ABSENT → Flow-local 暴露 → A1 PRESENT 成立时，才允许升级：

```text
ABSENT_TO_PRESENT_DURING_FLOW_LOCAL_EXPOSURE=CONFIRMED
NETWORK_PATH_DURING_TRANSITION=EXCLUDED
```

即便如此：

```text
FLOW_LOCAL_FINAL_SCORE_CALCULATOR=UNPROVEN
```

设计仍须区分：

- Flow 自行计算/写入评分
- 还是 Flow 触发了设备 firmware 计算。

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

# P0 — First-Writer 研究报告

**2026-08-15** · [English](P0_FIRST_WRITER_2026-08-15.md) · 简体中文

> [!NOTE]
> 这是一份脱敏的公开研究报告。私有 RAW 工件、设备标识、session ID 与个人 evidence 归档均有意省略。

## 1. 目标

确定哪个组件最先产生设备侧睡眠评分文件：

```text
/U/0/<date>/SLEEPSCO/SLEEPSCO.BPB
```

候选：

1. 设备 firmware
2. Polar Flow 本地处理
3. 远程/网络处理

## 2. 安全边界

整条研究线期间：

```text
PFTP_WRITE=0
PFTP_DELETE=0
BOND_MUTATION=0
```

不写文件、不删除、不改动配对、不 factory reset、不做 firmware 操作。所有设备访问均为只读且受策略门控；Research Agent 构建未新增任何设备写入能力。

## 3. Research Agent 架构

Research Agent（`0.7.0-first-writer-observer`，`versionCode=14`）为 JOJI 增加了一个受策略门控的观测层：

- PC → 设备命令传输：ADB + `run-as` 应用私有 inbox/outbox；无 root、无网络。
- 命令与 session 绑定，按 `commandId` 幂等，每条响应在 PC 侧归档。
- `arm-first-writer` 仅设置 session 标志，无设备动作。
- 已 armed 的 session 禁止 `sync`、`reconnect-after-sync` 与 reset 逃逸。
- `first-writer-connect` 每 session 恰好一次连接尝试，对象为固定已知设备。
- 普通模式 current-day GET 被拒绝；first-writer 模式仅白名单放行一条 current-day 路径：`SLEEPSCO/SLEEPSCO.BPB`。
- session 以 `release` 收尾，再执行 `export`（export 是最后一步证据变更），生成确定性 manifest。

已验证能力：

- session 隔离
- commandId 幂等
- 受控 sync
- 受控 post-sync reconnect
- 动态只读 PFTP LIST
- 历史只读 PFTP GET
- RAW artifact 保全
- 手机侧 SHA-256、二进制安全 PC pull、PC 侧 SHA-256
- 手机/PC 字节同一性校验
- release → export → FINALIZED
- 确定性 manifest 校验

## 4. 证据时间线

仅使用公共时间粒度：

| 窗口 | 观察 |
|---|---|
| 2026-08-15 凌晨 | 同日 T0 PRE_SYNC 对照：SLEEPSCO absent；post-sync 对照：仍 absent。 |
| 2026-08-15 凌晨 | 开始约 6 小时 device-only 隔离（Flow 禁用、网络 OFF、JOJI 停止、bond 保留）。 |
| 2026-08-15 早晨 | 隔离结束；晨间 PRE_SYNC：SLEEPSCO 仍 absent；睡眠/醒来数据存在。 |
| 2026-08-15 傍晚 | True Flow-local 离线暴露（约 11m54s，网络 OFF，无手动同步）。 |
| 2026-08-15 傍晚 | Flow 冻结且 ACL=0 后，PRE-sync first-writer 观察：SLEEPSCO present；SLEEPSCO.BPB present。 |

## 5. 历史 RAW GET

已验证：

- historical SLEEPSCO.BPB：172 bytes
- phone size == PC size
- phone SHA256 == PC SHA256

Labels：

```text
HISTORICAL_RAW_GET_DEVICE_TEST=PASS
RAW_BYTES_PRESERVED_END_TO_END=CONFIRMED
PHONE_PC_BYTE_IDENTITY=CONFIRMED
```

## 6. 同日期 PRE/POST sync

2026-08-15 受控观察：

- T0 PRE_SYNC（Flow/网络隔离，JOJI sync=0）：SLEEPSCO absent。
- 同日对照：一次成功的 JOJI sync + 受控 reconnect 后：SLEEPSCO 仍 absent。

Labels：

```text
T0_PRE_SYNC_SLEEPSCO_ABSENT=CONFIRMED
20260815_PRE_SYNC_SLEEPSCO=ABSENT
20260815_POST_SYNC_SLEEPSCO=ABSENT
SAME_DATE_PRE_POST_SYNC_DIRECTORY_SET=IDENTICAL
SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED
```

> [!NOTE]
> `SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED` 仅限定于本次受控观察；不等于声称 sync 永远不可能产生 SLEEPSCO。

## 7. 隔夜 device-only 隔离

约六小时的受控窗口：

- Polar Flow 包禁用
- Wi-Fi OFF
- 移动数据 OFF
- JOJI 停止，JOJI sync=0
- Bluetooth ON
- bond 保留

晨间 PRE_SYNC 结果：SLEEPSCO 仍 absent，同时相关睡眠/醒来数据存在。

Label：

```text
SLEEPSCO_DEVICE_AUTONOMOUS_GENERATION_WITHIN_OBSERVED_WINDOW=NOT_OBSERVED
```

> [!IMPORTANT]
> 刻意不写成 `DEVICE_LOCAL_GENERATION=REJECTED` —— 生成可能延迟，或需要另一个触发条件。

## 8. Flow/ACL 观察

- Flow 被普通 force-stop 后可以自动重启。
- JOJI 进程不存在时，Loop LE ACL 依然可以存在。
- 受控观察中，force-stop 活跃的 Flow 之后 ACL 快速释放。
- `disable-user` 产生了稳定的 Flow 隔离状态。
- 手机网络 OFF 时，前台 Flow 依然可以建立 Loop BLE ACL。

Labels：

```text
FLOW_ACL_ASSOCIATION=STRONGLY_SUPPORTED
FLOW_OWNS_ACL=UNPROVEN
FLOW_CAUSED_ACL=UNPROVEN
```

## 9. Flow-local 离线暴露

2026-08-15，傍晚：

- Polar Flow 明确置于前台
- Wi-Fi OFF
- 移动数据 OFF
- 无手动 Flow 同步点击
- JOJI sync=0
- 暴露时长：约 11m54s

全程观察：

```text
FLOW_PROCESS_OBSERVED=YES
FLOW_FOREGROUND_ACTIVE=YES
FLOW_ACL_OBSERVED=YES
```

Flow 被禁用且 ACL 回到 N 后，JOJI first-writer 连接成功。PRE JOJI-sync 观察：

- SLEEPSCO 目录 present
- SLEEPSCO.BPB present
- RAW GET 恰好一次：172 bytes，phone/PC 字节同一性 PASS

Labels：

```text
FLOW_LOCAL_OFFLINE_EXPOSURE=CONFIRMED
FLOW_CAN_ESTABLISH_LOOP_BLE_ACL_WITH_NETWORK_OFF=CONFIRMED
POST_FLOW_EXPOSURE_SLEEPSCO_PRESENT=CONFIRMED
POST_FLOW_EXPOSURE_RAW_SLEEPSCO_BPB=CONFIRMED
POST_FLOW_EXPOSURE_PRE_JOJI_SYNC=CONFIRMED

SLEEPSCO_TRANSITION_DURING_FLOW_EXPOSURE=UNPROVEN
FLOW_LOCAL_CAUSED_SLEEPSCO_APPEARANCE=UNPROVEN
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
```

## 10. 证据修正

明确记录的方法学修正：

- 距 Flow-local 暴露最近一次被确认的 ABSENT 快照，在暴露前数小时。
- 因此不存在紧邻暴露前的快照。
- 此前把 SLEEPSCO 的产生直接归因于 Flow-local 执行的因果解释，已撤回。

该修正作为 P0 记录的一部分保留。

## 11. 当前结论

```text
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
SLEEPSCO_TRANSITION_DURING_FLOW_EXPOSURE=UNPROVEN
FLOW_LOCAL_CAUSED_SLEEPSCO_APPEARANCE=UNPROVEN
```

P0 计算 / first-writer 归属仍未解决。

当前 evidence 不利于“JOJI sync 立即产生 SLEEPSCO”这类简化假设 —— 但**不**拒绝设备 firmware、Flow 本地处理或远程/网络路径；三者均未被现有 evidence 排除。

## 12. 下一步决定性实验

紧邻 A/B 设计（见 [`ROADMAP_CN.md`](ROADMAP_CN.md)）：

- **A0**：Flow 禁用、网络 OFF、JOJI sync=0、PRE_SYNC first-writer LIST —— 要求 `SLEEPSCO=ABSENT`。
- **B**：网络保持 OFF；启用 Flow 并置于前台；无手动同步；有界 local-only 暴露；随后禁用 Flow 并等待 ACL=N。
- **A1**：JOJI first-writer LIST，sync=0。

只有 A0 ABSENT → Flow-local 暴露 → A1 PRESENT 成立时，才允许升级：

```text
ABSENT_TO_PRESENT_DURING_FLOW_LOCAL_EXPOSURE=CONFIRMED
NETWORK_PATH_DURING_TRANSITION=EXCLUDED
```

即便如此，`FLOW_LOCAL_FINAL_SCORE_CALCULATOR=UNPROVEN` —— 设计仍须区分：是 Flow 自行计算/写入评分，还是 Flow 触发了设备 firmware 计算。

---

相关：[`RESEARCH_CN.md`](RESEARCH_CN.md) · [`BUILD_STATUS.md`](BUILD_STATUS.md)

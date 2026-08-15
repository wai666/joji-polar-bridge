# 研究记录

[English](RESEARCH.md) · **简体中文**

> [!NOTE]
> 批注标签：**Verified / 已验证** = 有本项目已记录 evidence 支持；**Observation / 观察** = 实验中直接看到但有范围限制；**Hypothesis / 假设** = 仍需更多验证的工作解释；**Policy / 策略** = JOJI 自己设定的边界。

## P0 — SLEEPSCO first-writer 研究

### 5A — 研究问题

首要问题：哪个组件最先产生：

```text
/U/0/<date>/SLEEPSCO/SLEEPSCO.BPB
```

候选：

1. 设备 firmware
2. Polar Flow 本地处理
3. 远程/网络处理

状态：

```text
P0_DEVICE_VS_FLOW_LOCAL=UNRESOLVED
REMOTE_FINAL_SCORE_CALCULATOR=NOT_PROVEN
DEVICE_FIRMWARE_FINAL_SCORE_CALCULATOR=NOT_PROVEN
```

### 5B — Research Agent 方法学

first-writer observer 设计：

- `arm-first-writer`：仅设置 session 标志，无任何设备动作。
- 已 armed 的 session 禁止：`sync`、`reconnect-after-sync`、reset 逃逸。
- `first-writer-connect`：每个 session 恰好一次尝试；固定已知设备；不存在不受限 connect API。
- 普通模式下 current-day GET：`DENY`。
- first-writer 模式：仅允许精确白名单内的 current-day 路径 `SLEEPSCO/SLEEPSCO.BPB`。
- 不存在任意 current-date GET。

### 5C — 历史 RAW GET 里程碑

实机验证：

```text
historical SLEEPSCO.BPB: 172 bytes
phone size == PC size
phone SHA256 == PC SHA256
```

Labels：

```text
HISTORICAL_RAW_GET_DEVICE_TEST=PASS
RAW_BYTES_PRESERVED_END_TO_END=CONFIRMED
PHONE_PC_BYTE_IDENTITY=CONFIRMED
```

### 5D — 同日期 PRE/POST sync

2026-08-15 受控观察。

T0 PRE_SYNC：

- Flow/网络隔离条件
- JOJI sync=0
- 列出 current-date 目录
- SLEEPSCO absent

Label：

```text
T0_PRE_SYNC_SLEEPSCO_ABSENT=CONFIRMED
```

随后同日对照：

- 一次成功的 JOJI sync
- 受控 reconnect
- 再次列出目录
- SLEEPSCO 仍 absent

Labels：

```text
20260815_PRE_SYNC_SLEEPSCO=ABSENT
20260815_POST_SYNC_SLEEPSCO=ABSENT
SAME_DATE_PRE_POST_SYNC_DIRECTORY_SET=IDENTICAL
SYNC_IMMEDIATE_SLEEPSCO_CREATION=NOT_SUPPORTED
```

> [!NOTE]
> 这**不**等于声称 sync 永远不可能产生 SLEEPSCO。

### 5E — 隔夜 device-only 隔离

约六小时的受控窗口：

- Polar Flow 包已禁用
- Wi-Fi OFF
- 移动数据 OFF
- JOJI 已停止
- JOJI sync=0
- Bluetooth ON
- bond 保留

次日早晨 PRE_SYNC 观察：SLEEPSCO 仍 absent，同时相关睡眠/醒来数据存在。

Label：

```text
SLEEPSCO_DEVICE_AUTONOMOUS_GENERATION_WITHIN_OBSERVED_WINDOW=NOT_OBSERVED
```

> [!IMPORTANT]
> 这里刻意**不**写成 `DEVICE_LOCAL_GENERATION=REJECTED`：生成可能延迟，或需要另一个触发条件。

### 5F — Flow / ACL 行为

观察：

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

### 5G — True Flow-local 离线暴露

2026-08-15：

- Polar Flow 明确置于前台
- Wi-Fi OFF
- 移动数据 OFF
- 无任何手动 Flow 同步点击
- JOJI sync=0
- 暴露时长：约 11m54s

全程观察：

```text
FLOW_PROCESS_OBSERVED=YES
FLOW_FOREGROUND_ACTIVE=YES
FLOW_ACL_OBSERVED=YES
```

Flow 被禁用且 ACL 回到 N 之后，JOJI first-writer 连接成功。

PRE JOJI-sync 观察：

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

### 5H — 证据修正

方法学修正，明确记录：

- 距 Flow-local 暴露最近一次被确认的 ABSENT 快照，在暴露前数小时。
- 因此不存在紧邻暴露前的快照。
- 此前把 SLEEPSCO 的产生直接归因于 Flow-local 执行的因果解释，已撤回。

## 设备家族

**Observation / 观察。** 当前 Polar Loop Gen 2 研究对象在本项目记录中呈现出与 JOJI 观察到的较新设备平台相关的标识，而不是旧 LOOP / LOOP2 家族。

**Hypothesis / 假设。** 这种平台差异可能解释为什么旧 Loop 代际的行为不能直接套用到当前设备。除非有独立官方文档支持，否则不应把它写成厂商兼容性声明。

## PMD / 实时数据

**Verified / 已验证（仅限项目范围）。** JOJI 早期阶段在严格限制并发的策略下，记录到了 skin temperature、PPI、accelerometer 和 PPG 在线流成功工作。

这只能证明已测试的 JOJI 路径与设备状态，不能推出所有 firmware / app 组合都具有完全相同的行为。

## 设备侧文件生命周期

**Observation / 观察。** 差分实验记录到不同设备文件与 SDK 数据存在明显不同的生命周期表现：

- HR / PPI / AUTOS / sleep-intermediate 文件在部分同步窗口间会变化或滚动；
- activity sample 与 daily summary 在观察窗口中表现出不同的持久化行为；
- history/index 文件可以更新，但其变化节奏与类似队列的数据并不一致。

**Hypothesis / 假设。** 部分频繁变化的数据可能具有滚动队列或“同步后消费”的特征。

> [!IMPORTANT]
> 上述生命周期解释来自 JOJI 当前 evidence，属于研究假设。它**不是**对 Polar 未公开协议的官方断言，也不保证跨设备、firmware 或 Flow 版本成立。

## 当前待研究的瞬态问题

**Observation / 观察。** A3 evidence 捕获过一次 RAW inventory protobuf 解析错误：`invalid tag (zero)`；后续运行又成功。

**Hypothesis / 假设。** 该故障更可能是瞬态或阶段局部问题，而不能据此断定底层数据永久不可读。

**计划验证：**

- 将 inventory 失败限制在本阶段；
- 保留此前已提交的本地数据；
- 不重新执行无关的已完成工作；
- 明确记录 retry / recovery evidence；
- 长期关联 RAW path 的变化。

## USB 研究分支

**Policy / 策略。** PC-USB 分支继续坚持 passive-first。公开研究在考虑任何协议操作前，只做 enumeration / interface 层面的检查。

当前计划明确排除 hidden mode activation、driver replacement、DFU 和 vendor-command 实验。

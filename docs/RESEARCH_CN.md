# 研究记录

[English](RESEARCH.md) · **简体中文**

> [!NOTE]
> 批注标签：**Verified / 已验证** = 有本项目已记录 evidence 支持；**Observation / 观察** = 实验中直接看到但有范围限制；**Hypothesis / 假设** = 仍需更多验证的工作解释；**Policy / 策略** = JOJI 自己设定的边界。

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

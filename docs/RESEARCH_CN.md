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

## RAW inventory 瞬态问题

**Observation / 观察。** A3 evidence 捕获过一次 RAW inventory protobuf 解析错误：`invalid tag (zero)`；后续运行又成功。

**Policy / 策略。** 本地 processing / recovery 应把此类故障限制在对应 stage，保留此前已经提交的数据，并明确记录 retry/recovery evidence。

## USB 研究分支

**Verified / 已验证（仅限当前测试设备与已记录 session）。** SDK 暴露的 USB connection setting 会作为 Windows host 枚举的软件 gate。setting ON 时，Windows 枚举出 `VID_0DA4:PID_0014` composite USB device，并观察到一个绑定系统 `usbser` 的 CDC ACM 接口；恢复 OFF 后该枚举消失。

**Verified / 已验证。** 通过 CDC ACM serial transport，以 115200 8N1 + RTS/CTS 执行固定只读 Polar PFTP `GET /DEVICE.BPB` 成功，返回 protobuf 与当前测试 Loop Gen 2 的固件/型号系列一致。公开仓库不发布个人 device ID 等用户标识。

**Verified / 已验证 — 受控 filesystem mapping。** 仅使用 directory listing 的 PFTP read，已经成功确认 `/`、`/U/`、`/U/0/`、`/U/0/<日期>/`（早期 research window 中）。单个每日目录 listing 确认了 `SKINCONT/` 和 `SKINTEMP/` 子目录。在后来的独立 research window 中，同一 `<日期>/` 路径及其 `SKINTEMP/` 子目录分别返回 PFTP error 103（NO_SUCH_FILE_OR_DIRECTORY），尽管 transport 和 request framing 均已验证正确。随后 `/U/0/` 重新 listing 确认较早日期条目已从索引中消失，较晚日期条目已出现。所有观察到的非日期条目保持不变。公开文档不会发布用户专属日期名或标识。

**Observation / 观察。** `/U/0/` 下日期状目录条目表现出动态索引成员关系。较早日期条目从索引中消失与其路径返回 PFTP error 103 在时序上相关。保留/生命周期机制仍未解决。

**Observation / 观察。** Windows USB bus descriptor product string 与 `DEVICE.BPB` 内部 model name 是不同层级的标识。JOJI 将其分别记录，不再假设二者可互换。

**Policy / 策略。** USB 工作默认继续保持 fixed-path + read-only。未经过新的 reviewed gate，不允许目录递归、任意文件读取、driver replacement、DFU、firmware write、raw vendor-command exploration 或 device-file mutation。本分支唯一实际执行过的 device-side setting mutation 是专用、可逆的 USB connection-mode toggle，并要求 pre-read、即时 readback 与 OFF restore。

详细证据：[`../research/usb/USB_EVIDENCE_20260810_CN.md`](../research/usb/USB_EVIDENCE_20260810_CN.md)

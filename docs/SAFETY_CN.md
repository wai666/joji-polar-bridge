# 安全边界

[English](SAFETY.md) · **简体中文**

> [!IMPORTANT]
> **Policy / 策略：** 以下规则是 JOJI 为本研究项目自行设定的安全边界，不作为 Polar 官方要求来表述。

项目把现有 Polar Loop 当作正在使用的个人设备，而不是可随意破坏的测试目标。

## 不变量

- 复用现有 Android bond。
- 不创建或删除 bond。
- 不 reset / factory-reset 设备。
- 不发起 firmware 操作。
- 不启用 SDK mode。
- 不修改 offline recording 状态。
- 不启动 ECG。
- 不执行 SDK `readFile`。
- 不调用设备文件 write/delete API。
- 不修改设备 log configuration。
- 不发送手工/raw 设备文件修改命令。
- 除非确认存在缺陷且经过 review，否则保持封存的 V4.3/R4 connection recovery state machine。

## 经审核的 USB 例外

项目不再声称“从未发生任何 device-setting write”。USB 研究分支已经把 SDK USB connection-mode setting 作为一个**可逆、边界明确的例外**执行过：

- 先读取当前值；
- 只有 requested state 与 baseline 不同时才写入；
- 写入后立即 readback；
- 实验结束后恢复 USB mode OFF；
- 不与 firmware、file write/delete、reset、bond 或 log-configuration mutation 混用。

该例外**不代表**允许任意持久设备 mutation。

## 文件访问

BLE 侧可执行的设备文件获取继续限制在此前 review 过的 fixed path / whitelist。公开仓库不发布个人设备 payload。

USB 研究分支可在独立 reviewed gate 中，对固定 path 使用 PFTP `GET` 做只读协议验证。目录 mapping 默认只允许 single-level、non-recursive，除非后续 gate 明确扩展。公开 artifacts 继续排除 user identifier 与个人 payload。

## 释放行为

成功 BLE session 应以 callback-confirmed disconnect 结束。访问受保护设备路径时出现 permission denial，应视为边界信号，而不是需要绕过的障碍。USB research window 完成后应恢复 USB mode OFF；只有在同一个明确、受控的 read-only session 紧接着执行下一 gate 时才暂时保持 ON。

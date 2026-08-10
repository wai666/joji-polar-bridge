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

## 文件访问

可执行的文件获取严格限制在此前审核过的 V4.3 whitelist。公开仓库不发布个人设备 payload。

## 释放行为

成功会话应以 callback-confirmed disconnect 结束。访问受保护设备路径时出现 permission denial，应视为边界信号，而不是需要绕过的障碍。

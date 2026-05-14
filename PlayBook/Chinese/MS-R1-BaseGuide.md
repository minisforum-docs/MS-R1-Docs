# MS-R1

## 介绍
MS-R1 是一款基于 ARM 的迷你主机，与许多 Android 手机采用相同架构。由于架构相同，MS-R1 可以在 Docker 中运行 Android，比传统 x86 Android 模拟器具有更好的兼容性。

MS-R1 的 CPU 基于 CIX CP8180，属于 ARM v9 架构，搭配 Arm Immortalis-G720 MC10 GPU。CPU 多核性能几乎相当于 Intel Core i5-12450H，GPU 性能相当于 AMD Radeon 680M 集成显卡。

CP8180 支持 AV1、H.265、H.264 视频解码，并支持 H.265、H.264 视频编码。

CP8180 支持 KVM，可实现高效虚拟化，同时支持 PCIe 直通（passthrough）。

## 如何使用 MS-R1
- [如何安装 PVE](./MS-R1-How-To-Install-PVE.md)
- [重新安装系统](./MS-R1-How-To-Reinstall-OS.md)
- [如何在 Docker 中运行 Android](./MS-R1-How-To-Run-Android-In-Docker.md)
- [如何在 Docker 中运行 Jellyfin](./MS-R1-Jellyfin-Docker-Compose.md)
- [CPU DEBUG命令](.MS-R1-DebugCommands.md)
- [优化待机功耗](./MS-R1-IdlePower.md)
- [ARM System Ready专用的OS和BIOS](./MS-R1-ARMSystemReady.md)


## 基本信息

### 默认用户名 / 密码
- 用户名：`mini`
- 密码：`mini`

### 禁用小核心以优化性能

根据当前测试，小核心的性能和内部互连带宽远低于大/中核心。在小核心上运行多核工作负载会显著降低整体性能。除非你需要额外的核心用于虚拟化或类似工作负载，否则建议在正常使用时禁用小核心。

#### 如何禁用小核心
1. 重启并进入 BIOS（启动时按 `Del` 或 `Esc`）。
2. 进入 `System Manager` → `SOC Configuration` → `CPU Configuration`。
3. 禁用 CPU 核心 `2`、`3`、`4` 和 `5`。
4. 保存更改并退出 BIOS（通常为 `Save & Exit`），然后重启。

注意：如果需要为虚拟机或其他大型多核任务使用额外的 CPU 核心，请重新启用这些核心。

### I/O 端口

#### 前置 I/O

| 端口 / 按钮 | 说明 |
|-------------|------|
| 1× 电源按钮 | 用于开/关机。长按 5 秒强制关机。 |
| 1× 3.5mm 复合接口 | 标准 4 针复合插孔（CTIA 标准）。 |
| 1× USB 3.2 Gen 2 | 10 Gbps Type-A USB3 接口 |
| 2× USB 2.0 | 480 Mbps USB2 Type-A 接口 |

#### 后置 I/O

| 端口 / 按钮 | 说明 |
|-------------|------|
| 2× USB 2.0 | 480 Mbps USB2 Type-A 接口 |
| 2× RJ45 | 10 Gbps RJ45 以太网口（基于 RTL8127） |
| 2× Type-C | 全功能 Type-C 接口（USB 最高 10 Gbps，DP 1.4）。非 USB4。 |
| 1× HDMI | HDMI 2.0 接口 |
| 2× USB 3.2 Gen 2 | 10 Gbps Type-A USB3 接口 |
| 1× 电源输入 | 5.5×2.5 mm 19V 电源输入 |



#### 内部 I/O

##### 背面

| 端口 / 按钮 | 说明 |
|-------------|------|
| 1× 40-pin GPIO | 引脚定义待定 |
| 1× 自动开机开关 | ON：接通电源时自动开机 |
| BIOS 烧录引脚 & UART1 引脚 | 烧录引脚连接到 BIOS 闪存。主板上标注有 UART1 引脚 |
| 1× U.2 电源 | U.2 的 12V 电源 |
| 1× M.2 E-key | PCIe 4.0 x2，带 USB |
| 1× M.2 M-key | PCIe 4.0 x4 |
| 1× TPM 引脚 | 预留的 TPM 接口 |
| 1× 5V 风扇接头 | 5V，用于 SSD 风扇 |
| 1× RTC 接口 | 实时时钟连接器 |

##### CPU 侧

| 端口 / 按钮 | 说明 |
|-------------|------|
| 1× UART2 | 主板标注有 UART2 引脚 |
| 1× CPU 风扇 | CPU 散热风扇接头 |
| 1× PCIe 插槽 | PCIe 4.0 x8（不支持分线/分叉 bifurcation） |

## 另见
- [如何安装 PVE](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [如何在 Docker 中运行 Android](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)




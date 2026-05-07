# MS-R1 的 ARM System Ready OS 和 BIOS

## 什么是 ARM SystemReady？

[ARM SystemReady](https://www.arm.com/architecture/system-architectures/systemready-compliance-program) 是 Arm 推出的合规认证计划，确保平台能够开箱即用地启动标准操作系统和虚拟机监控程序——无需供应商特定的修改。获得认证的设备保证任何兼容的操作系统（Linux 发行版、虚拟机监控程序等）都可以像在标准 x86 PC 上一样安装和运行。

MS-R1 是经过 ARM SystemReady 认证的平台。您可以在 [Arm SystemReady 合作伙伴页面](https://www.arm.com/architecture/system-architectures/systemready-compliance-program/partners)上验证这一点。

这意味着您可以在 MS-R1 上使用标准的 ARM SystemReady BIOS 和通用的 ARM64 Linux 文件系统，而不是被锁定在供应商特定的操作系统镜像中。

## 下载

MS-R1 的 ARM SystemReady BIOS 和 Linux 文件系统可在以下位置获取：

**Google Drive：** https://drive.google.com/drive/folders/1FC8yqZLZhQOBfQ_oB137jjGcnfNVM7da

| 文件 | 说明 |
|------|------|
| `MS-R1-20260312-ARMSystemReady-BIOS.zip` | MS-R1 的 ARM SystemReady 兼容 BIOS 固件 |
| `linux-fs-20260312.7z` | 通用 ARM64 Linux 文件系统镜像 |

## 重要说明

### 推荐的 BIOS 设置

为了获得最佳性能和兼容性，在刷入 ARM SystemReady BIOS 后，建议禁用 CPU 核心 2-5。请参阅基础指南中的[禁用小核心](./MS-R1-BaseGuide.md#禁用小核心以优化性能)获取详细说明。

### Windows 兼容性说明

> **注意：** 如果您打算在 MS-R1 上使用 Windows（不是主要目标用户群，但可用于开发/实验目的），除了禁用核心 2-5 外，还必须将 **LPI 最大状态设置为 LPI0**。
>
> 这是满足 Arm BSA 规则 **B_PE_01：检查 PE 之间的架构对称性**所必需的。Windows 强制执行严格的 PE 对称性，如果违反此规则，将无法启动或行为异常。
>
> **步骤：**
> 1. 进入 BIOS（启动时按 `Del` 或 `Esc`）。
> 2. 进入 `System Manager` → `SOC Configuration` → `CPU Configuration`。
> 3. 禁用 CPU 核心 `2`、`3`、`4` 和 `5`。
> 4. 将 **LPI max state** 设置为 `LPI0`。
> 5. 保存并重启。
>
> Linux 不需要此设置。

## 刷入 BIOS

请参阅[重新安装系统](./MS-R1-How-To-Reinstall-OS.md)了解一般刷入步骤。使用 `MS-R1-20260312-ARMSystemReady-BIOS.zip` 文件替代标准 BIOS 镜像。

## 另见

- [基础指南](./MS-R1-BaseGuide.md)
- [重新安装系统](./MS-R1-How-To-Reinstall-OS.md)
- [Arm SystemReady 合规计划](https://www.arm.com/architecture/system-architectures/systemready-compliance-program)

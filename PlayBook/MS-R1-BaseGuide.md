# MS-R1

## Intro
MS-R1 is an ARM-based mini PC that shares the same architecture as many Android phones.
Because of the shared architecture, MS-R1 can run Android in Docker with much better compatibility than traditional x86 Android emulators.

MS-R1 CPU based on CIX CP8180, a ARM V9 architecture CPU with Arm Immortalis-G720 MC10 GPU.
The CPU multi-core performance almost equa to Intel's Core i5 12450H. And GPU performance equal to AMD's Radeon 680M iGPU.

CP8180 Supports AV1,H265,H264 video decode and H265,H265 video encode.

CP8180 supported KVM which allows highly efficient virtualization. It's also support PCIE passthough.

## How To Play MS-R1
- [How to Install PVE](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [How To Reinstall OS](/PlayBook/MS-R1-How-To-Reinstall-OS.md) — Reinstall OS
- [How To Run Android Use Docker](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)
- [How To Run Jellyfin Use Docker](../PlayBook/MS-R1-Jellyfin-Docker-Compose.md)
- [CPU Debug Commands](../PlayBook/MS-R1-DebugCommands.md)
- [Optimize Idle Power](../PlayBook/MS-R1-IdlePower.md)
- [ARM SystemReady OS and BIOS](../PlayBook/MS-R1-ARMSystemReady.md)

## Basic Information

### Default username / password
- Username: `mini`
- Password: `mini`

### Disable small cores (optimize performance)

Based on current tests, the small cores have much lower performance and internal interconnect bandwidth than the big/medium cores. Running multi-core workloads on the small cores can significantly reduce overall performance. Unless you need the extra cores for virtualization or similar workloads, it is recommended to disable the small cores for normal use.

#### How to disable small cores
1. Reboot and enter BIOS (press `Del` or `Esc` during boot).
2. Go to `System Manager` → `SOC Configuration` → `CPU Configuration`.
3. Disable CPU Core `2`, `3`, `4`, and `5`.
4. Save changes and exit BIOS (usually `Save & Exit`), then reboot.

Note: Re-enable the cores if you require additional CPU cores for VMs or other heavy multi-core tasks.  

### I/O Ports

#### Front I/O

| Ports / Buttons | Note |
|-----------------|------|
| 1× Power button | To turn on/off the MS-R1. Press 5s to force shutdown. |
| 1× 3.5mm combo jack | Standard 4-pin combo jack (CTIA standard). |
| 1× USB 3.2 Gen 2 | 10 Gbps Type-A USB3 port |
| 2× USB 2.0 | 480 Mbps USB2 Type-A ports |


#### Back I/O

| Ports / Buttons | Note |
|-----------------|------|
| 2× USB 2.0 | 480 Mbps USB2 Type-A ports |
| 2× RJ45 | 10 Gbps RJ45 Ethernet ports (RTL8127 based) |
| 2× Type-C | Full-function Type-C ports (USB up to 10 Gbps, DP 1.4). Not USB4. |
| 1× HDMI | HDMI 2.0 port |
| 2× USB 3.2 Gen 2 | 10 Gbps Type-A USB3 ports |
| 1× Power IN | 5.5×2.5 mm 19V power input |


#### Internal I/O

##### Back side

| Ports / Buttons | Note |
|-----------------|------|
| 1× 40-pin GPIO | Pin definitions TBD |
| 1× Auto power-on switch | ON: auto power on when power is connected |
| BIOS flash pin & UART1 pin | Flash pin connects to BIOS flash. UART1 pins are printed on motherboard |
| 1× U.2 power | 12V power for U.2 |
| 1× M.2 E-key | PCIe 4.0 x2 with USB |
| 1× M.2 M-key | PCIe 4.0 x4 |
| 1× TPM pin | Reserved TPM connector |
| 1× 5V fan connector | 5V, for SSD fan |
| 1× RTC connector | Real-time clock connector |

##### CPU side

| Ports / Buttons | Note |
|-----------------|------|
| 1× UART2 | UART2 pins printed on motherboard |
| 1× CPU fan | CPU cooling fan connector |
| 1× PCIe port | PCIe 4.0 x8 (does not support bifurcation) |

## See also
- [How to Install PVE](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [How To Run Android In Docker](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)




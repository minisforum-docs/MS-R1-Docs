# MS-R1 Operating System Reinstallation Guide

## Introduction

The MS-R1 uses a standard UEFI BIOS that can boot any UEFI-compatible system ISO. While the CPU drivers are not yet fully merged into the mainstream Linux Kernel, MINISFORUM's official system image provides the best user experience.

> **Note**: Once the drivers are merged into the mainstream Linux Kernel, you'll be able to use any operating system, similar to a regular x86 PC.

## System Requirements

- Minimum 40GB storage space
- USB drive or SSD for system installation
- Internet connection for downloading the system image

## Installation Steps

### 1. Download System Image

Latest stable version: `MS-R1-2025101202-0003301.7z`

For better idle power: `2025-11-15-PowerSAVE`

Download from: [Google Drive Link](https://drive.google.com/drive/folders/1FzjO50RYqKi-BIOYH2wduRYeeju-MRIU?usp=drive_link)

### 2. Write Image to System Disk

#### Windows Users
1. Download and install `balenaEtcher`
2. Unzip the system image
3. Use `balenaEtcher` to write the image to your system disk

#### Linux Users
1. Unzip the system image
2. Use the `dd` command to write the image to your system disk

### 3. System Access

Default credentials:
- Username: `mini`
- Password: `mini`

## Additional Resources

### Open Source Code
The system's source code is available on GitHub: [minisforum-cix-p1-repo](https://github.com/orgs/minisforum-cix-p1-repo)

### Related Documentation
- [MS-R1 Base Guide](../PlayBook/MS-R1-BaseGuide.md)
- [PVE Installation Guide](../PlayBook/MS-R1-How-To-Install-PVE.md)
- [Running Android in Docker](../PlayBook/MS-R1-How-To-Run-Android-In-Docker.md)

> **Warning**  
> Ensure you have backed up all important data before proceeding with the system installation.




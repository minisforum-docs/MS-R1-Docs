# MS-R1 — 在 Docker 中运行 Android

## 概要
MS-R1 是一款基于 ARM 的迷你电脑，与许多 Android 设备使用相同的架构。在 MS-R1 上通过 Docker 运行 Android，比传统的 x86 仿真器具有更好的兼容性。本简要指南说明如何准备环境并运行 Android 容器。

---

## 前提条件
- 运行在 Debian/Ubuntu 系的 MS-R1（命令假定为 Debian/Ubuntu）。
- 具有 root 权限（使用 `sudo`）。
- 如果你已经在 MS-R1 上安装了 Proxmox VE（PVE），则可跳过文档中标注的 PVE 专用准备步骤。
- 已安装 `unzip`。

---

## 为 Android 准备 Docker 环境

1. 切换为 root：
    ```bash
    sudo -i
    ```

2. （仅针对需要 legacy iptables 的系统）将 iptables 切换为 legacy 后端以避免 Docker 网络问题：
    ```bash
    update-alternatives --set iptables /usr/sbin/iptables-legacy
    update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
    ```
    说明：如果你已经配置了 PVE 或者发行版使用与 nftables 兼容的 Docker 构建，可跳过此步骤。

3. 安装 Docker：
    ```bash
    apt update
    apt install -y docker.io unzip
    ```

4. 验证 Docker 是否运行：
    ```bash
    docker ps
    # 预期（无容器时）输出："CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS    PORTS   NAMES"
    ```

5. 可选：允许你的用户在不使用 sudo 的情况下运行 Docker：
    ```bash
    # 将 <your-user> 替换为你的用户名
    usermod -aG docker <your-user>
    # 注销并重新登录（或重启）以使组更改生效
    ```

---

## 下载 Docker Android 包
目前提供预构建的 Docker 镜像。构建过程后续可能会开源。

### 下载 "MS-R1-Docker-Android-2025-10.zip"
链接：
https://drive.google.com/file/d/1ZmzPTqV8TrBDLLztjCfDC1WhnGQs7WaJ/view?usp=sharing

### 导入 docker 镜像
将 `MS-R1-Docker-Android-2025-10.zip` 上传或复制到 MS-R1 并解压：
```bash
unzip MS-R1-Docker-Android-2025-10.zip
```
使用脚本导入 docker 镜像：
```bash
cd MS-R1-Docker-Android-2025-10
chmod +x *.sh
./load_super_image.sh sky1_evb-14-userdebug-super.img.tgz
```
如果出现类似 `Error: No such image: cix_android:14` 或 `Error: No such image: 14` 的错误，请忽略它们。

## 启动 Docker Android

```bash
./run_android.sh -c 0 -d 0 -m 0 -v 14
```
你可以运行多个 Android 容器，例如：
```bash
# 第一个 Android 容器 
run_android.sh -c 0 -d 0 -m 0 -v 14
# 第二个 Android 容器 
run_android.sh -c 1 -d 2 -m 0 -v 14
# 第三个 Android 容器 
run_android.sh -c 2 -d 4 -m 0 -v 14
```


## 使用 "Escrcpy" 连接到 Android 并开始使用

下载与你的系统和架构匹配的最新版 Escrcpy 并安装：  
https://github.com/viarotel-org/escrcpy/releases/  
例如：  
| 系统与架构 | 下载文件名 |
|----------|--------------|
| Windows X86 PC | [Escrcpy-1.32.1-win-setup-x64.exe](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-win-setup-x64.exe)|
| Old X86 Mac |  [Escrcpy-1.32.1-mac-x64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-x64.dmg) | 
| ARM Based Mac | [Escrcpy-1.32.1-mac-arm64.dmg](https://github.com/viarotel-org/escrcpy/releases/download/v1.32.1/Escrcpy-1.32.1-mac-arm64.dmg) | 

在连接界面输入你的 IP 地址。端口 = 10000 +（容器编号）。例如：

| Android 容器编号 | 端口 |
|----------|--------------|
| 0 | 10000 |
| 1 | 10001 | 
| 2 | 10002 |
# MS-R1 How To Install NVIDIA's Driver

This Guide is For How to Install NVIDIA's CUDA Only Drvier to MS-R1's factory Debian System.

Dud to not stable on NVIDIA's X11 Support.

## Installation Steps

### Remove CIX's GPU Package. 
```bash
# Remove cix-libglvnd which conflict with NV's GPU Driver
apt autoremove cix-libglvnd

# Stop GDM, NV's driver will upgrade GPU libs.
systemctl stop gdm
```
### Install cix-genksyms.deb
Download "cix-genksyms.deb" from: [Google Drive Link](https://drive.google.com/file/d/1XN1P79it_PzAe9HzKx4KSFkSsUpJ542X/view?usp=drive_link)  

Upload it and install it.

```bash
dpkg -i --force-overwrite cix-genksyms.deb
```

### Download NV's CUDA 12 Package And Install it

```
wget 'https://developer.download.nvidia.com/compute/cuda/12.8.0/local_installers/cuda_12.8.0_570.86.10_linux_sbsa.run'

chmod +x cuda_12.8.0_570.86.10_linux_sbsa.run

./cuda_12.8.0_570.86.10_linux_sbsa.run --no-opengl-libs --no-drm --- --no-opengl-files --no-drm --no-install-libglvnd
```

Accept NV's license agreement.  
Select Install. And just wait install finished.

### Check Driver Installed
Using `nvidia-smi` to check driver and CUDA works fine.
```
root@mini-localhost:~# nvidia-smi 
Sun Nov 23 19:01:40 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.86.10              Driver Version: 570.86.10      CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA RTX 2000 Ada Gene...    Off |   00000000:C1:00.0 Off |                  N/A |
| N/A   45C    P3            590W /   60W |       1MiB /   8188MiB |      3%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```
systemctl start gdm

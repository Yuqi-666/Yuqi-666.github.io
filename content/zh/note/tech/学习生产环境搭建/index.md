---
private: false
featured: false
title: 学习生产环境搭建
authors:
  - admin
tags:
  - 环境配置
categories:
  - tech
summary: 可复现的学习生产环境搭建教程
---
{{< toc mobile_only=true is_open=true >}}

## WSL2

**安装Ubuntu24.04发行版**
```bash
wsl --install Ubuntu
wsl --set-default Ubuntu
wsl
```
**修改登录用户为root**
1. 修改wsl.conf
```bash
sudo nano /etc/wsl.conf
```
2. 将用户名改为root
```text
[user]
default=root
```
**更换apt源**
1. 用vscode打开wsl
2. 打开文件/etc/apt/sources.list.d/ubuntu.sources
3. 将系统源替换为
```text
Types: deb
URIs: http://mirrors.tuna.tsinghua.edu.cn/ubuntu/
Suites: noble noble-updates noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```
4. 更新apt
```bash
apt update
```
## Podman
```bash
apt install podman
pip3 install podman-compose
```
## Nvidia container toolkit
参考https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```bash
sudo apt-get update
```

```bash
export NVIDIA_CONTAINER_TOOLKIT_VERSION=1.17.8-1
  sudo apt-get install -y \
      nvidia-container-toolkit=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      nvidia-container-toolkit-base=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container-tools=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container1=${NVIDIA_CONTAINER_TOOLKIT_VERSION}
```

**为podman配置CDI**
1. 生成CDI规范文件
```bash
sudo nvidia-ctk cdi generate --output=/etc/cdi/nvidia.yaml
```
2. 检查生成的设备名称
```bash
nvidia-ctk cdi list
```
3. 使用示例
```bash
podman run --rm --device nvidia.com/gpu=all --security-opt=label=disable ubuntu nvidia-smi -L
```
## git

```bash
# 设置用户名
git config --global user.name "Your Name"

# 设置邮箱（建议与代码托管平台账号邮箱一致）
git config --global user.email "your.email@example.com"
```
## 交叉环境
```bash
sudo apt install -y qemu-user-static binfmt-support
sudo systemctl restart binfmt-support
```

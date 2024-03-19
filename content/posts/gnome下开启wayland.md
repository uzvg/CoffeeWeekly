---
# 文章标题
title: "Gnome下开启wayland"
# 文章内容摘要
description: "gnome下开启wayland"
# 文章内容关键字
keywords: "gnome下开启wayland"
# 发表日期
date: 2024-03-19T16:40:39+08:00
# 最后修改日期
lastmod: 2024-03-19T16:40:39+08:00
# 分类
categories:
  - Linux
# 标签
tags:
  - Linux
  - ArchLinux
  - Gnome
  - Wayland

# 原文作者
author: uzvg
# 原文链接
#link:
# 图片链接，用在open graph和twitter卡片上
#imgs:
# 在首页展开内容
#expand: true
# 外部链接地址，访问时直接跳转
#extlink:
# 在当前页面关闭评论功能
#comment:
# enable: false
# 关闭当前页面目录功能
# 注意：正常情况下文章中有H2-H4标题会自动生成目录，无需额外配置
#toc: false
# 绝对访问路径
url: "gnome下开启wayland.html"
# 开启文章置顶，数字越小越靠前
#weight: 1
# 开启数学公式渲染，可选值： mathjax, katex
#math: mathjax
# 开启各种图渲染，如流程图、时序图、类图等
#mermaid: true
---

确保安装`nvidia-dkms`
```bash
sudo pacman -S nvidia-dkms
```
编辑`gdm custom.conf`
```bash
sudo gedit /etc/gdm/custom.conf
WaylandEnable=true # Enable wayland on Gnome
```
将NVIDIA驱动添加到内核启动模块：
```
sudo vim /etc/mkinitcpio.conf
# 在模块MODULES中添加以下参数：
MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
# 重新生成initramfs
sudo mkinitcpio -P
```
`mkinitcpio`: `mkinitcpio` 是一个用于创建 initramfs 的工具。它负责收集和打包所需的文件、驱动程序和工具，以便它们在启动时可以被 Linux 内核加载。
`-P`: `--allpresents`,选项表示重新生成 initramfs 文件。该选项会读取`/etc/mkinitcpio.d/`文件夹下的所有预设配置文件，并依此重新生成initramfs文件。
比如`linux-zen.preset`
```bash
# mkinitcpio preset file for the 'linux-zen' package

ALL_config="/etc/mkinitcpio.conf"
ALL_kver="/boot/vmlinuz-linux-zen"

# PRESETS=('default' 'fallback')
PRESETS=('default')

#default_config="/etc/mkinitcpio.conf"
default_image="/boot/initramfs-linux-zen.img"
#default_options=""

#fallback_config="/etc/mkinitcpio.conf"
fallback_image="/boot/initramfs-linux-zen-fallback.img"
fallback_options="-S autodetect"
```
默认情况下，通过preset文件，mkinitcpip会生成fallback备用镜像，但在添加NVIDIA内核模块后，通过`sudo mkinitcpio -P`产生的`initramfs`文件，即`/boot/initramfs-linux-zen.img`会明显增大，可能会造成`boot`分区的空间不足，如果遇到这种情况，就可以通过修改配置文件，禁止其生成fallback镜像。
Disable GDM udev rules which force the use of Xorg and reboot
```bash
sudo ln -s /dev/null /etc/udev/rules.d/61-gdm.rules
```
拓展：
`initramfs`的作用是什么？
Linux中的`initramfs`是用于在启动Linux内核之前提供一个轻量级的临时文件系统，以支持加载必要的驱动程序和设置，特别是在引导过程中需要解密根文件系统或进行其他关键操作时。
`/etc/mkinitcpio.conf`中各个选项的作用是什么？
`mkinitcpio.conf` 文件是 Arch Linux 中 `mkinitcpio` 工具的配置文件，用于生成初始 RAM 环境。以下是一些常见选项的解释：
1. `MODULES`：指定要包括在初始 RAM 环境中的内核模块列表。这些模块通常是用于引导和初始化硬件设备的。
2. `BINARIES`：列出要包含在初始 RAM 环境中的二进制文件。这些文件通常是用于初始化和挂载文件系统的工具。
3. `FILES`：指定要包括在初始 RAM 环境中的其他文件。这可以包括自定义脚本、配置文件等。
4. `HOOKS`：定义要执行的挂载点和动作。这些挂载点和动作按顺序执行，用于初始化和准备系统以引导。
5. `COMPRESSION`：指定生成的初始 RAM 环境映像的压缩格式。通常是 gzip。
6. `COMPRESSION_OPTIONS`：包含与压缩相关的选项。
这些选项允许你自定义生成的初始 RAM 环境，以适应特定的系统要求。你可以根据需要添加或删除内核模块、二进制文件和挂载点，以确保系统能够成功引导。
`/etc/mkinitcpio.conf`文件中的`hooks`数组的作用：`HOOKS=(base udev autodetect keyboard keymap modconf block filesystems fsck)`
`mkinitcpio.conf` 文件中的 `HOOKS` 配置指定了用于生成初始 RAM 环境 (initramfs) 的一系列挂载点和动作。在你提供的示例中，以下是每个挂载点/动作的作用：
1. `base`：这是初始 RAM 环境的基础，包括内核模块的加载和文件系统的初始化。
2. `udev`：用于初始化 `udev`，这是 Linux 中的设备管理系统。它有助于检测和管理硬件设备。
3. `autodetect`：这个挂载点的作用是自动检测和加载所需的硬件驱动程序和内核模块。
4. `keyboard`：用于初始化键盘支持，确保在引导过程中能够与键盘进行交互。
5. `keymap`：加载键盘映射，以确保键盘的布局和字符映射正确。
6. `modconf`：这个挂载点用于自动检测并加载与硬件相关的内核模块。
7. `block`：用于初始化块设备支持，以便在引导过程中能够访问磁盘。
8. `filesystems`：加载文件系统支持，以便在引导后能够挂载根文件系统。
9. `fsck`：该挂载点用于文件系统检查，确保文件系统的完整性。
总的来说，`HOOKS` 的配置确定了在初始 RAM 环境中执行的步骤，以便在引导过程中能够成功加载所需的驱动程序、初始化设备并准备文件系统。这个配置根据系统的需要可以进行自定义，以确保系统能够成功引导。
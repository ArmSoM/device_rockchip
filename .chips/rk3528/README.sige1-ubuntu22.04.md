# Sige1 V1.2: Ubuntu 22.04 + Linux 6.1

Ubuntu 22.04（Jammy，arm64）与 Debian 12 共用已经移植的 Sige1 Linux 6.1 设备树和内核配置。板载接口和 40PIN 可选 overlay 见同目录 `README.sige1-debian12.md`。

在 SDK 根目录执行，以下为 GNOME 版本：

```sh
./build.sh armsom_sige1_rk3528_ubuntu_gnome_defconfig
./build.sh kernel-6.1
./build.sh ubuntu
./build.sh firmware
./build.sh updateimg
```

需要 XFCE 或无桌面版本时，将配置名中的 `gnome` 换为 `xfce` 或 `server`。

- 设备树：`kernel-6.1/arch/arm64/boot/dts/rockchip/rk3528-armsom-sige1.dts`。
- 内核配置：`kernel-6.1/arch/arm64/configs/armsom_linux_rk3528_defconfig`。
- 首次启动脚本已增加 Sige1 识别，选择对应 DTB 和 `uEnvarmsom-sige1.txt`。
- 沿用 Ubuntu 目录现有 Mali-450 用户态库、SYN43752 Wi-Fi/蓝牙固件和 RTL8125 固件；RK3528 镜像补充安装 `wireless-regdb`。无线驱动来自内核，初始化沿用 rootfs 内的 `wifibt-init.sh`。
- rootfs 使用专用名称 `ubuntu-rk3528-sige1-6.1-<桌面>-rootfs.img`。首次使用需重新生成 rootfs，包含本次初始化脚本修改。
- SDK 也会复用 `ubuntu22.04/ubuntu-<桌面>-rootfs.img`；若存在此通用缓存，请先改名保存再构建。修改脚本或内核后再次构建时，也需移走相应专用 rootfs 缓存，避免沿用旧镜像。

配置名中的 `ubuntu` 指向 Ubuntu 22.04；与 Debian 12 的六个菜单项均使用 Linux 6.1。旧 SDK 配置归档已删除；`kernel-5.10/` 原有设备树及源码保留。本次只进行配置解析及脚本静态检查，完整编译、烧录和硬件验证由使用者完成。

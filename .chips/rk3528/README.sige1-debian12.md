# Sige1 V1.2: Debian 12 + Linux 6.1

本配置依据 `ARMSOM-SIGE1_V1.2_SCH_20240727.pdf`，保留原 Linux 5.10 设备树及源码。当前改动只做了配置解析和静态检查，内核编译、烧录及接口实测由使用者完成。

## 构建

在 SDK 根目录执行，以下为 GNOME 版本：

```sh
./build.sh armsom_sige1_rk3528_debian_gnome_defconfig
./build.sh kernel-6.1
./build.sh debian
./build.sh firmware
./build.sh updateimg
```

需要 XFCE 或无桌面版本时，将配置名中的 `gnome` 换为 `xfce` 或 `server`。

- 设备树：`kernel-6.1/arch/arm64/boot/dts/rockchip/rk3528-armsom-sige1.dts`
- 内核配置：`kernel-6.1/arch/arm64/configs/armsom_linux_rk3528_defconfig`
- 新 rootfs 名称为 `linaro-rk3528-sige1-6.1-<桌面>-rootfs.img`，避免直接复用旧的 `linaro-rk3528-<桌面>-rootfs.img`。首次使用本配置需重新生成 rootfs，才能包含新的板型初始化逻辑。
- 若另有通用的 `debian12/linaro-<桌面>-rootfs.img`，SDK 也会优先复用它；需要先自行改名保存，再生成新 rootfs。

## 板载接口

| 接口 | 配置 |
| --- | --- |
| 千兆网口 | GMAC1 + RTL8211F，复位 GPIO4_B5，PHY 地址 1 |
| 2.5G 网口 | PCIe 2.0 x1 + RTL8125，复位 GPIO4_A4，r8169 驱动 |
| Wi-Fi / 蓝牙 | BW3752-50B1 / SYN43752，SDIO0 + UART2 M1；沿用 SDK 的 bcmdhd、蓝牙初始化工具和已有固件 |
| USB | EHCI/OHCI + FE1.1s HUB 接两个 USB2 Host；Type-C 为 USB2 OTG，VBUS 由 GPIO1_B4 控制 |
| 显示 / 音频 | HDMI 输出及 HDMI 音频；SAI2 + 内置 ACODEC 接 3.5mm 耳机口 |
| 存储 | 8-bit eMMC HS400、TF 卡；TF 电源 GPIO4_A1，电压切换 GPIO4_C2 |
| RTC | I2C0 地址 0x51，HYM8563 兼容器件，中断 GPIO4_A0 |
| 散热 / 指示 | PWM6 M2 风扇，绿灯 GPIO3_B3，红灯 GPIO3_B2 |
| 调试 | UART0 M0，1500000 波特率；40PIN 8/10 脚 |

GPU 与 VDD_LOGIC 共用 PWM2 调压电源；CPU 使用 PWM3。保留原有 OPP，不新增超频档位。风扇按原理图的反相驱动使用低有效 PWM。电源及温控行为仍需烧录后实测。

## 40PIN 可选功能

默认开启 I2C1（3/5 脚）、I2C0（27/28 脚，与 RTC 共用总线）以及 UART0 调试串口。其余已引出的 GPIO 保持可按需使用。

在板端 `/boot/uEnv/uEnvarmsom-sige1.txt` 中取消对应行的注释，再重启：

```ini
# SPI0: MOSI=19, MISO=21, SCLK=23, CS0=24, CS1=26
#dtoverlay=/dtb/overlay/rk3528-armsom-sige1-spi0-overlay.dtbo
# I2S1: SCLK=12, LRCK=35, SDO0=40, SDI0=23
#dtoverlay=/dtb/overlay/rk3528-armsom-sige1-i2s1-overlay.dtbo
# PWM4: pin 33
#dtoverlay=/dtb/overlay/rk3528-armsom-sige1-pwm4-overlay.dtbo
# PWM5: pin 32
#dtoverlay=/dtb/overlay/rk3528-armsom-sige1-pwm5-overlay.dtbo
```

SPI0 与 I2S1 共用 GPIO4_B4，只选择其中一个。I2S1 的 pin 38 路径需要焊接原理图中标为 NC 的 R3116；按默认装配，SDI0 在 pin 23。I2S1 未引出 MCLK，其对应 GPIO4_A4 已用于板载 PCIe 复位；外接音频器件需支持使用 BCLK/LRCK，必要时再按该器件绑定替换通用数字音频节点。

配置名不带版本号：`debian` 指向 Debian 12，`ubuntu` 指向 Ubuntu 22.04，六项均使用 Linux 6.1。旧 SDK 配置归档已删除；`kernel-5.10/` 原有设备树及源码保留。

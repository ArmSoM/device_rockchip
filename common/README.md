# Automatically build firmware
```shell
   ./build.sh chip
```
After selecting the configuration file, a pink prompt font will be used to indicate the kernel version used in the current configuration file
One click compilation of u-Boot, kernel, Rootfs and packaging as an update.img image
```shell
   ~$ ./build.sh
```
The constructed image is saved in the `rockdev/` directory

# Build firmware step by step
When developing firmware, it is recommended to use partition building and the functionality of building individual modules using SDK.

Select SDK configuration file
```shell
   ~$ ./build.sh LubanCat_rk3588_debian_gnome_defconfig
```
## U-Boot construction
```shell
   ~$ ./build.sh uboot
```
## Kernel construction
Boot partition kernel image, first generate kernel deb package, then compile the kernel and package the generated deb package into the boot partition.
```shell
   ~$ ./build.sh kernel
```
## Rootfs construction
Building Debian
```shell
   ~$ ./build.sh debian
```
Building Ubuntu
```shell
   ~$ ./build.sh ubuntu
```

## Image packaging
Firmware packaging
```shell
./build.sh firmware
```
Generate update.img
```shell
./build.sh updateimg
```

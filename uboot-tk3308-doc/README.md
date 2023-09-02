# U-boot build instructions
## See below for latest u-boot v2023.10-rc3
U-boot tag: uboot v2022.04
patch: `001-rock-pi-s-files.patch`

- Prepare idbloader.img

```bash
export BL31=/home/ubuntu/bins/rk3308_bl31_v2.26.elf
make rock-pi-s-rk3308_defconfig
make -j12 CROSS_COMPILE=aarch64-linux-gnu- all
./tools/mkimage -n rk3308 -T rksd -d /home/ubuntu/bins/rk3308_ddr_589MHz_uart0_m0_v2.06.bin idbloader.img 
cat /home/ubuntu/bins/rk3308_miniloader_v1.39.bin >> idbloader.img 
cp idbloader.img /home/ubuntu/rockpis/package/boot/uboot-rockchip/src/blob/
```


- Prepare uboot.img and trust.img (rockchip/rkbin repo required)

```bash
./tools/loaderimage --pack --uboot /home/ubuntu/u-boot/u-boot.bin uboot.img 0x00600000
cp uboot.img /home/ubuntu/rockpis/package/boot/uboot-rockchip/src/blob/
```

- trust.img is not required to be generated every time
```bash
./tools/trust_merger RKTRUST/RK3308TRUST.ini 
cp trust.img /home/ubuntu/rockpis/package/boot/uboot-rockchip/src/blob/
```

# Compiling v2023.10-rc3

u-boot tag: uboot v2023.10-rc3
patch: `0001-fix-boot-in-rock-pi-s.patch`


- Apply above patch with `git apply ...`
- Prepare idbloader.img

```bash
export BL31=..../rk3308_bl31_v2.26.elf
make rock-pi-s-rk3308_defconfig
make -j12 CROSS_COMPILE=aarch64-linux-gnu- all
./tools/mkimage -n rk3308 -T rksd -d ..../rk3308_ddr_589MHz_uart0_m0_v2.06.bin idbloader.img 
cat spl/u-boot-spl.bin >> idbloader.img
cp idbloader.img u-boot.itb ../openwrt/package/boot/uboot-rockchip/src/blob/
```
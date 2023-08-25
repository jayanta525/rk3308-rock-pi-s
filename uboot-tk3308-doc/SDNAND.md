# NAND Install Guide

Requirements:
- rkdeveloptool
- linux environment with root access


### SD NAND is basically NAND flash with SD controller.




## Steps

1. Power on the Rock Pi S without any SD Card
1. Hold down Maskrom key and press Reset key
1. Release Maskrom key
1. Prepare `openwrt....img` (gunzip) and loader file `rk3308_loader_ddr589MHz_uart0_m0_v2.06.136sd.bin`

```
sudo rkdeveloptool ld                                                       #check if device present
sudo rkdeveloptool db rk3308_loader_ddr589MHz_uart0_m0_v2.06.136sd.bin      #write loader
sudo rkdeveloptool wl 0 openwrt.....img                                     #write image

# board expects the sd-card to be connected, or else won't boot.
# if sd-card is connected and detected, board boots from nand successfully
# no-idea why this behaviour, workaround below

sudo rkdeveloptool ul rk3308_loader_ddr589MHz_uart0_m0_v2.06.136sd.bin      #upgrade loader
sudo rkdeveloptool rd                                                       #reboot
```

## Note

After a sysupgrade, the loader on the NAND will be replaced. Board will fail to boot without sd-card.

The workaround can be applied again, but only `rkdeveloptool ul` command is required.

## A permanent solution to this - 

rename  `idbloader-nand.img` to `idbloader.img`, replacing existing `idbloader.img` in `openwrt/packages/boot/uboot-rockchip/src/blob/`

- idbloader-nand.img -> This will generate images bootable from SD-NAND
- idbloader.img -> This will only boot from SD-CARD.

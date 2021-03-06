#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_VERSION:=2020.07
PKG_RELEASE:=2

PKG_HASH:=c1f5bf9ee6bb6e648edbf19ce2ca9452f614b08a9f886f1a566aa42e8cf05f6a

PKG_MAINTAINER:=Tobias Maedel <openwrt@tbspace.de>

include $(INCLUDE_DIR)/u-boot.mk
include $(INCLUDE_DIR)/package.mk

define U-Boot/Default
  BUILD_TARGET:=rockchip
  UENV:=default
  HIDDEN:=1
endef

# RK3308 boards

define U-Boot/rock-pi-s-rk3308
  BUILD_SUBTARGET:=armv8
  NAME:=Rock Pi S
  BUILD_DEVICES:= \
    radxa_rock-pi-s
  DEPENDS:=+PACKAGE_u-boot-rock-pi-s-rk3308:arm-trusted-firmware-rockchip
  PKG_BUILD_DEPENDS:=arm-trusted-firmware-rockchip
  ATF:=rk3308_bl31.elf
  OF_BLOB:=$(1)
endef

# RK3328 boards

define U-Boot/nanopi-r2s-rk3328
  BUILD_SUBTARGET:=armv8
  NAME:=NanoPi R2S
  BUILD_DEVICES:= \
    friendlyarm_nanopi-r2s
  DEPENDS:=+PACKAGE_u-boot-nanopi-r2s-rk3328:arm-trusted-firmware-rockchip
  PKG_BUILD_DEPENDS:=arm-trusted-firmware-rockchip
  ATF:=rk3328_bl31.elf
  OF_PLATDATA:=$(1)
endef


# RK3399 boards

define U-Boot/rockpro64-rk3399
  BUILD_SUBTARGET:=armv8
  NAME:=RockPro64
  BUILD_DEVICES:= \
    pine64_rockpro64
  DEPENDS:=+PACKAGE_u-boot-rockpro64-rk3399:arm-trusted-firmware-rockchip
  PKG_BUILD_DEPENDS:=arm-trusted-firmware-rockchip
  ATF:=rk3399_bl31.elf
endef

UBOOT_TARGETS := \
  rockpro64-rk3399 \
  rock-pi-s-rk3308 \
  nanopi-r2s-rk3328

UBOOT_CONFIGURE_VARS += USE_PRIVATE_LIBGCC=yes

UBOOT_MAKE_FLAGS += \
  BL31=$(STAGING_DIR_IMAGE)/$(ATF)

define Build/Configure
	$(call Build/Configure/U-Boot)

ifneq ($(OF_PLATDATA),)
	mkdir -p $(PKG_BUILD_DIR)/tpl/dts
	mkdir -p $(PKG_BUILD_DIR)/include/generated

	$(CP) $(PKG_BUILD_DIR)/of-platdata/$(OF_PLATDATA)/dt-platdata.c $(PKG_BUILD_DIR)/tpl/dts/dt-platdata.c
	$(CP) $(PKG_BUILD_DIR)/of-platdata/$(OF_PLATDATA)/dt-structs-gen.h $(PKG_BUILD_DIR)/include/generated/dt-structs-gen.h
endif

	$(SED) 's#CONFIG_MKIMAGE_DTC_PATH=.*#CONFIG_MKIMAGE_DTC_PATH="$(PKG_BUILD_DIR)/scripts/dtc/dtc"#g' $(PKG_BUILD_DIR)/.config
	echo 'CONFIG_IDENT_STRING=" OpenWrt"' >> $(PKG_BUILD_DIR)/.config
endef

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)

ifneq ($(OF_BLOB),)
	$(CP) $(PKG_BUILD_DIR)/blobs/$(OF_BLOB)/idbloader.bin $(PKG_BUILD_DIR)/idbloader.bin
	$(CP) $(PKG_BUILD_DIR)/blobs/$(OF_BLOB)/uboot.img $(PKG_BUILD_DIR)/uboot.img
	$(CP) $(PKG_BUILD_DIR)/blobs/$(OF_BLOB)/trust.bin $(PKG_BUILD_DIR)/trust.bin
endif

	$(CP) $(PKG_BUILD_DIR)/idbloader.bin $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-idbloader.bin
	$(CP) $(PKG_BUILD_DIR)/uboot.img $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-uboot.img
	$(CP) $(PKG_BUILD_DIR)/trust.bin $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-trust.bin
endef

define Package/u-boot/install/default
endef

$(eval $(call BuildPackage/U-Boot))

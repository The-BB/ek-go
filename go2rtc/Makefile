# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=go2rtc
PKG_VERSION:=1.9.9
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_SOURCE_URL:=https://github.com/AlexxIT/go2rtc
PKG_MIRROR_HASH:=c7043bdee3e0af270fb95c694e09673780b381641ea7a07032db46fadbfbfd6b

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Entware team, https://entware.net

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/go2rtc/Default
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=go2rtc camera streaming
  URL:=https://github.com/AlexxIT/go2rtc
  DEPENDS:=+ffmpeg
endef

define Package/go2rtc
  $(call Package/go2rtc/Default)
  VARIANT:=hf
endef

define Package/go2rtc_nohf
  $(call Package/go2rtc/Default)
  VARIANT:=nohf
  DEPENDS+=@arm
endef

define Package/go2rtc/description
 Ultimate camera streaming application with support RTSP, RTMP,
 HTTP-FLV, WebRTC, MSE, HLS, MP4, MJPEG, HomeKit, FFmpeg, etc.
endef

Package/go2rtc_nohf/description=$(Package/go2rtc/description)

define Package/go2rtc/conffiles
/opt/etc/go2rtc.yaml
endef

Package/go2rtc_nohf/conffiles=$(Package/go2rtc/conffiles)

define Build/Prepare
	$(call Build/Prepare/Default)
  ifeq ($(CONFIG_mips),y)
	$(SED) '/v4l2/d' $(PKG_BUILD_DIR)/main.go
  endif
endef

define Package/go2rtc/install
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S98go2rtc $(1)/opt/etc/init.d
	$(INSTALL_DATA) ./files/go2rtc.yaml $(1)/opt/etc
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/go2rtc $(1)/opt/sbin
endef

Package/go2rtc_nohf/install=$(Package/go2rtc/install)

$(eval $(call BuildPackage,go2rtc))
$(eval $(call BuildPackage,go2rtc_nohf))

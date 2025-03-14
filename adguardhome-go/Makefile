# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2019-2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=adguardhome
PKG_VERSION:=0.107.57
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2025-02-20
PKG_SOURCE_VERSION:=805de598050b93b02f60b85ba0ac268c830f7255
PKG_SOURCE_URL:=https://github.com/AdguardTeam/AdGuardHome
PKG_MIRROR_HASH:=32b139d8a2dce5850020888eeb9baf3e425fc8d1a84e6d01c292e5941825b558

PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE.txt

PKG_BUILD_DEPENDS:=node-yarn/host
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/adguardhome-go/Default
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Network-wide ads & trackers blocking DNS server
  URL:=https://adguard.com/adguard-home.html
  MAINTAINER:=Entware team, https://entware.net
  DEPENDS:=+ca-bundle
endef

define Package/adguardhome-go
  $(call Package/adguardhome-go/Default)
  VARIANT:=hf
endef

define Package/adguardhome-go_nohf
  $(call Package/adguardhome-go/Default)
  VARIANT:=nohf
  DEPENDS+=@arm
endef

define Package/adguardhome-go/description
  AdGuard Home is a network-wide software for blocking ads and tracking.
endef

Package/adguardhome-go_nohf/description=$(Package/adguardhome-go/description)

define Package/adguardhome-go/conffiles
/opt/etc/AdGuardHome/adguardhome.conf
endef

Package/adguardhome-go_nohf/conffiles=$(Package/adguardhome-go/conffiles)

GO_LDFLAGS += \
	-X '$(XIMPORTPATH)/internal/version.channel=release' \
	-X '$(XIMPORTPATH)/internal/version.version=v$(PKG_VERSION)' \
	-X '$(XIMPORTPATH)/internal/version.committime=$(PKG_SOURCE_DATE)'

define Build/Compile
	( cd $(PKG_BUILD_DIR); \
		NODE_OPTIONS=--openssl-legacy-provider \
		$(MAKE) js-deps js-build; \
	)
	( cd $(PKG_BUILD_DIR); $(GO_BIN_MOD_TIDY); )
	$(call Build/Compile/Go)
endef

define Package/adguardhome-go/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/AdGuardHome $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S99adguardhome $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/etc/AdGuardHome
	$(INSTALL_CONF) ./files/adguardhome.conf $(1)/opt/etc/AdGuardHome
endef

Package/adguardhome-go_nohf/install=$(Package/adguardhome-go/install)

$(eval $(call BuildPackage,adguardhome-go))
$(eval $(call BuildPackage,adguardhome-go_nohf))

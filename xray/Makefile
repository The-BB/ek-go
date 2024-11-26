# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2021-2024 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=xray
PKG_VERSION:=24.11.21
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=513f18bf531ef5f92464c2affefed0db4f28280b
PKG_SOURCE_URL:=https://github.com/XTLS/Xray-core
PKG_MIRROR_HASH:=cff3f4353820f540e809c1885cfdb5d79ab5f24d3988b6f5612a43316d53c4a7

PKG_LICENSE:=MPL-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/xray/Default
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Web Servers/Proxies
	TITLE:=a personal vpn server
	URL:=https://github.com/XTLS/Xray-core
	MAINTAINER:=Entware team, https://entware.net
	DEPENDS:=+ca-bundle
endef

define Package/xray-core
	$(call Package/xray/Default)
	VARIANT:=hf
endef

define Package/xray-core_nohf
	$(call Package/xray/Default)
	VARIANT:=nohf
	DEPENDS+=@arm
endef

define Package/xray
	$(call Package/xray/Default)
	TITLE+= (scripts & configs)
	VARIANT:=hf
	DEPENDS:=+xray-core
endef

define Package/xray_nohf
	$(call Package/xray/Default)
	TITLE+= (scripts & configs)
	VARIANT:=nohf
	DEPENDS:=+xray-core_nohf
endef

define Package/xray-core/description
 Project X originates from XTLS protocol, providing a set of network tools.
endef

Package/xray-core_nohf/description=$(Package/xray-core/description)

define Package/xray/description
 scripts & configs.
endef

Package/xray_nohf/description=$(Package/xray/description)

define Package/xray/conffiles
/opt/etc/xray/config.json
endef

Package/xray_nohf/conffiles=$(Package/xray/conffiles)

XIMPORTPATH:=$(call tolower,$(XIMPORTPATH))

GO_LDFLAGS += \
	-X '$(XIMPORTPATH)/core.build=$(COMMIT_SHORT)'

GO_TARGET:=./main

define Package/xray-core/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/main $(1)/opt/sbin/xray
endef

Package/xray-core_nohf/install=$(Package/xray-core/install)

define Package/xray/install
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S24xray $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/etc/xray
	$(INSTALL_CONF) ./files/config.json.example $(1)/opt/etc/xray
endef

Package/xray_nohf/install=$(Package/xray/install)

$(eval $(call BuildPackage,xray-core))
$(eval $(call BuildPackage,xray-core_nohf))
$(eval $(call BuildPackage,xray))
$(eval $(call BuildPackage,xray_nohf))

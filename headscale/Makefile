# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2023-2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=headscale
PKG_VERSION:=0.25.1
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_SOURCE_URL:=https://github.com/juanfont/headscale
PKG_MIRROR_HASH:=1a256780f1e8654be51e91b6aa5f1cbecea67739a5f296e3d5074fe5f850e4f4

PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/headscale/Default
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=VPN
	TITLE:=Tailscale control server
	URL:=https://github.com/juanfont/headscale
	MAINTAINER:=Entware team, https://entware.net
	DEPENDS:=+ca-bundle @!(mips||mipsel)
endef

define Package/headscale
	$(call Package/headscale/Default)
	VARIANT:=hf
endef

define Package/headscale_nohf
	$(call Package/headscale/Default)
	VARIANT:=nohf
	DEPENDS+=@arm
endef

define Package/headscale/description
 Headscale aims to implement a self-hosted, open source alternative
 to the Tailscale control server. Headscale's goal is to provide self-hosters
 and hobbyists with an open-source server they can use for their projects and
 labs. It implements a narrow scope, a single Tailnet, suitable for a personal
 use, or a small open-source organisation.
endef

Package/headscale_nohf/description=$(Package/headscale/description)

define Package/headscale/conffiles
/opt/etc/headscale/config.yaml
endef

Package/headscale_nohf/conffiles=$(Package/headscale/conffiles)

GO_LDFLAGS += \
	-X '$(XIMPORTPATH)/cmd/headscale/cli.Version=v$(PKG_VERSION)'

GO_BUILD_ARGS += -a

GO_TARGET:=./cmd/headscale

define Package/headscale/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/headscale $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/headscale
	$(CP) $(PKG_BUILD_DIR)/config-example.yaml $(1)/opt/etc/headscale/config.yaml
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S07headscale $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/var/{lib,run}/headscale
endef

Package/headscale_nohf/install=$(Package/headscale/install)

$(eval $(call BuildPackage,headscale))
$(eval $(call BuildPackage,headscale_nohf))

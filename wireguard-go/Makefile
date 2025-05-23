# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2011-2024 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=wireguard-go
PKG_VERSION:=0.0.20230223
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=e24fc776e0ffb6e3c293895163d95bdd8a3c386f
PKG_SOURCE_URL:=https://git.zx2c4.com/wireguard-go
PKG_MIRROR_HASH:=13193587b99f70b152f6312dd55c749177e5e9ecafb642c8e36b63382462261e

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/wireguard-go/Default
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=VPN
	TITLE:=This is an implementation of WireGuard in Go
	URL:=https://git.zx2c4.com/wireguard-go/about/
	MAINTAINER:=Entware team, https://entware.net
	DEPENDS:=+wireguard-tools +wg-quick
endef

define Package/wireguard-go
	$(call Package/wireguard-go/Default)
	VARIANT:=hf
endef

define Package/wireguard-go_nohf
	$(call Package/wireguard-go/Default)
	VARIANT:=nohf
	DEPENDS+=@arm
endef

define Package/wireguard-go/description
 This is an implementation of WireGuard in Go.
endef

Package/wireguard-go_nohf/description=$(Package/wireguard-go/description)

# XXX link: golang.org/x/net/internal/socket: invalid reference to syscall.recvmsg
define PreBuild
	# fix go 1.23.x
	( cd $(PKG_BUILD_DIR); \
		$(GO_BIN_GET) -u golang.org/x/net \
		golang.zx2c4.com/wireguard/conn \
		golang.zx2c4.com/wireguard/device; \
	)
endef

Hooks/Compile/Pre += PreBuild

define Package/wireguard-go/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/wireguard $(1)/opt/bin/wireguard-go
endef

Package/wireguard-go_nohf/install=$(Package/wireguard-go/install)

$(eval $(call BuildPackage,wireguard-go))
$(eval $(call BuildPackage,wireguard-go_nohf))

# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2023-2024 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=kopia
PKG_VERSION:=0.17.0
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=89c8eb47af2e1d5c1d14fe299a0cf7eaac095abf
PKG_SOURCE_URL:=https://github.com/kopia/kopia
PKG_MIRROR_HASH:=d9bed3dc3a10884b83d444faea204eabcd2cc71799c073d41763ff92384ad246

PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/kopia/Default
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=Backup
  TITLE:=backup/restore tool
  URL:=https://kopia.io/
  MAINTAINER:=Entware team, https://entware.net
endef

define Package/kopia
  $(call Package/kopia/Default)
  VARIANT:=hf
endef

define Package/kopia_nohf
  $(call Package/kopia/Default)
  VARIANT:=nohf
  DEPENDS:=@arm
endef

define Package/kopia/description
  Kopia is a fast and secure open-source backup/restore tool that allows
  you to create encrypted snapshots of your data and save the snapshots
  to remote or cloud storage of your choice, to network-attached storage
  or server, or locally on your machine.
endef

Package/kopia_nohf/description=$(Package/kopia/description)

define Package/kopia/conffiles
/opt/etc/kopia/kopia.conf
endef

Package/kopia_nohf/conffiles=$(Package/kopia/conffiles)

ifneq ($(CONFIG_USE_MUSL),)
  define FixMusl
	# set rw or skip
	chmod +w -Rf $(DL_DIR)/go-mod-cache/github.com/hanwen/go-fuse/v2@v2.5.1 || true
	# rm
	rm -rf $(DL_DIR)/go-mod-cache/github.com/hanwen/go-fuse/v2@v2.5.1
	# get
	$(GO_BIN_MOD_DOWNLOAD) github.com/hanwen/go-fuse/v2@v2.5.1
	# set rw
	chmod +w -Rf $(DL_DIR)/go-mod-cache/github.com/hanwen/go-fuse/v2@v2.5.1
	# patch
	patch -p1 -d $(DL_DIR)/go-mod-cache < ./patches-mod/010-musl.patch
	# set ro
	chmod -w -Rf $(DL_DIR)/go-mod-cache/github.com/hanwen/go-fuse/v2@v2.5.1
  endef
  Hooks/Configure/Pre += FixMusl
endif

GO_LDFLAGS += \
	-X '$(XIMPORTPATH)/repo.BuildVersion=$(PKG_VERSION)' \
	-X '$(XIMPORTPATH)/repo.BuildInfo=$(COMMIT_SHORT)' \
	-X '$(XIMPORTPATH)/repo.BuildGitHubRepo=$(PKG_NAME)'

define Package/kopia/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/kopia $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/kopia
	$(INSTALL_DATA) ./files/kopia.conf $(1)/opt/etc/kopia
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S99kopia $(1)/opt/etc/init.d
endef

Package/kopia_nohf/install=$(Package/kopia/install)

$(eval $(call BuildPackage,kopia))
$(eval $(call BuildPackage,kopia_nohf))

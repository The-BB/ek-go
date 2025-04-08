# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2022-2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=alist
PKG_VERSION:=3.44.0
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2025-03-27
PKG_SOURCE_VERSION:=0cde4e73d614a4bf0b28872391a13d0f8a6d166a
PKG_SOURCE_URL:=https://github.com/AlistGo/alist
PKG_MIRROR_HASH:=b2380537f39a2740a3ea1e0dcf83fc008db92a7aef47eb729704fc063e6032c2

PKG_LICENSE:=AGPL-3.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1
PKG_CGO_ENABLED:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/alist/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Filesystem
  TITLE:=A file list program that supports multiple storage
  URL:=https://alist.nn.ci/
endef

define Package/alist
  $(call Package/alist/Default)
  VARIANT:=hf
endef

define Package/alist_nohf
  $(call Package/alist/Default)
  VARIANT:=nohf
  DEPENDS:=@arm
endef

define Package/alist/description
  A file list program that supports multiple storage.
endef

Package/alist_nohf/description=$(Package/alist/description)

WEB_VERSION:=$(PKG_VERSION)
WEB_FILE:=$(PKG_NAME)-web-$(WEB_VERSION).tar.gz
define Download/alist-web
  FILE:=$(WEB_FILE)
  URL_FILE:=dist.tar.gz
  URL:=https://github.com/AlistGo/alist-web/releases/download/$(WEB_VERSION)
  HASH:=3709bec59bbc14f0f9f74193cebbb25a317c978fa3a5ae06a900eb341e1b5ae7
endef

GIT_TAG:=v$(PKG_VERSION)-$(COMMIT_SHORT)
GO_VER:=$(shell $(GO_BIN) version | sed 's/go version //;s,/.*,/$(GOARCH),')

XIMPORTPATH:=$(subst AlistGo,alist-org,$(XIMPORTPATH))

GO_LDFLAGS += \
	-X '$(XIMPORTPATH)/v3/internal/conf.BuiltAt=$(PKG_SOURCE_DATE)' \
	-X '$(XIMPORTPATH)/v3/internal/conf.GoVersion=$(GO_VER)' \
	-X '$(XIMPORTPATH)/v3/internal/conf.GitAuthor=Entware' \
	-X '$(XIMPORTPATH)/v3/internal/conf.GitCommit=$(COMMIT_SHORT)' \
	-X '$(XIMPORTPATH)/v3/internal/conf.Version=$(PKG_VERSION)' \
	-X '$(XIMPORTPATH)/v3/internal/conf.WebVersion=$(WEB_VERSION)'

GO_TAGS:=jsoniter

define Build/Prepare
	$(call Build/Prepare/Default)
	$(HOST_TAR) -xf $(DL_DIR)/$(WEB_FILE) -C $(PKG_BUILD_DIR)/public
endef

define Package/alist/install
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S99alist $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/alist $(1)/opt/sbin
endef

Package/alist_nohf/install=$(Package/alist/install)

$(eval $(call Download,alist-web))
$(eval $(call BuildPackage,alist))
$(eval $(call BuildPackage,alist_nohf))

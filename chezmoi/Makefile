# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2023-2025 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=chezmoi
PKG_VERSION:=2.62.0
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2025-04-06
PKG_SOURCE_VERSION:=488650aeeb870635f488f8c4308c2ea3f2eba626
PKG_SOURCE_URL:=https://github.com/twpayne/chezmoi
PKG_MIRROR_HASH:=dfacc7932bfb122e34422e31a870edf17b47efbcdc41db6c31badd7864001b37

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/chezmoi/Default
	SECTION:=utils
	CATEGORY:=Utilities
	TITLE:=Manage dotfiles
	URL:=https://www.chezmoi.io
	MAINTAINER:=Entware team, https://entware.net
endef

define Package/chezmoi
	$(call Package/chezmoi/Default)
	VARIANT:=hf
endef

define Package/chezmoi_nohf
	$(call Package/chezmoi/Default)
	VARIANT:=nohf
	DEPENDS:=@arm
endef

define Package/chezmoi/description
  Manage your dotfiles across multiple diverse machines, securely.
endef

Package/chezmoi_nohf/description=$(Package/chezmoi/description)

GO_LDFLAGS += \
	-X 'main.version=v$(PKG_VERSION)' \
	-X 'main.commit=$(COMMIT_SHORT)' \
	-X 'main.date=$(PKG_SOURCE_DATE)' \
	-X 'main.builtBy=Entware'

GO_TAGS:=noupgrade

define Package/chezmoi/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/chezmoi $(1)/opt/bin
endef

Package/chezmoi_nohf/install=$(Package/chezmoi/install)

$(eval $(call BuildPackage,chezmoi))
$(eval $(call BuildPackage,chezmoi_nohf))

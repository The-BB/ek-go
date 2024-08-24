# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2024 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=fzf
PKG_VERSION:=0.54.3
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=af4917dbb643a7991c50091ec55ec25acb7d28cb
PKG_SOURCE_URL:=https://github.com/junegunn/fzf
PKG_MIRROR_HASH:=7ba80d368770c82d50d3539be24707327197962bb2956a88200ec805e7326e96

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Entware team, https://entware.net

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/fzf/Default
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=A command-line fuzzy finder
  URL:=https://github.com/junegunn/fzf
endef

define Package/fzf
  $(call Package/fzf/Default)
  VARIANT:=hf
endef

define Package/fzf_nohf
  $(call Package/fzf/Default)
  VARIANT:=nohf
  DEPENDS:=@arm
endef

define Package/fzf/description
  fzf is a general-purpose command-line fuzzy finder.
endef

Package/fzf_nohf/description=$(Package/fzf/description)

GO_LDFLAGS += \
	-X 'main.version=$(PKG_VERSION)' \
	-X 'main.revision=$(COMMIT_SHORT)'

define Package/fzf/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/fzf $(1)/opt/sbin
endef

Package/fzf_nohf/install=$(Package/fzf/install)

$(eval $(call BuildPackage,fzf))
$(eval $(call BuildPackage,fzf_nohf))

# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2023-2024 Entware

include $(TOPDIR)/rules.mk

PKG_NAME:=grafana
PKG_VERSION:=10.4.10
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2024-09-27
PKG_SOURCE_VERSION:=8afe3110090def4c990909693875e79a490dd5f4
PKG_SOURCE_URL:=https://github.com/grafana/grafana
PKG_MIRROR_HASH:=5b41f26a9796e9c8bc2d36d8111223ac03103e8bb7b8ea5039073b6a6d207d1b

PKG_LICENSE:=AGPL-3.0-only
PKG_LICENSE_FILES:=LICENSE LICENSING.md

PKG_BUILD_DEPENDS:=node-yarn/host
PKG_BUILD_PARALLEL:=1
PKG_CGO_ENABLED:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/golang.mk

define Package/grafana/Default
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=prometheus tools
  TITLE:=platform for monitoring and observability
  URL:=https://grafana.com
  MAINTAINER:=Entware team, https://entware.net
endef

define Package/grafana
  $(call Package/grafana/Default)
  VARIANT:=hf
endef

define Package/grafana_nohf
  $(call Package/grafana/Default)
  VARIANT:=nohf
  DEPENDS:=@arm
endef

define Package/grafana/description
  The open and composable observability and data visualization
  platform. Visualize metrics, logs, and traces from multiple sources like
  Prometheus, Loki, Elasticsearch, InfluxDB, Postgres and many more. 
endef

Package/grafana_nohf/description=$(Package/grafana/description)

GO_LDFLAGS += \
	-X 'main.version=$(PKG_VERSION)' \
	-X 'main.commit=$(COMMIT_SHORT)' \
	-X 'main.buildstamp=$(PKG_SOURCE_DATE)' \
	-X 'main.buildBranch=v10.4.x'

GO_TARGET:=./pkg/cmd/grafana ./pkg/cmd/grafana-cli ./pkg/cmd/grafana-server

define Build/Deps
	# install frontend dependencies
	( cd $(PKG_BUILD_DIR); \
		YARN_ENABLE_PROGRESS_BARS=false YARN_CHECKSUM_BEHAVIOR=update \
		HTTP_PROXY=localhost yarn install --immutable; \
	)
	# wire-host
	( cd $(PKG_BUILD_DIR)/.bingo; \
		$(GO_ENV_COMMON) $(GO_BIN) build -mod=mod -modfile=wire.mod \
		-o=$(PKG_BUILD_DIR)/wire-host "github.com/google/wire/cmd/wire"; \
	)
	( cd $(PKG_BUILD_DIR); \
		for p in \
			github.com/apache/arrow/go/v15/arrow/ipc@v15.0.0 \
			github.com/apache/arrow/go/v15/arrow@v15.0.0 \
			github.com/googleapis/gax-go/v2@v2.13.0 \
			github.com/grafana/grafana-google-sdk-go/pkg/tokenprovider@v0.1.0 \
			github.com/grafana/grafana/pkg/cmd/grafana \
			github.com/grafana/grafana/pkg/codegen \
			github.com/grafana/grafana/pkg/components/imguploader/gcs \
			github.com/grafana/grafana/pkg/extensions \
			github.com/grafana/grafana/pkg/login/social \
			github.com/grafana/pyroscope-go/godeltaprof/internal/pprof@v0.1.6 \
			github.com/kr/pretty@v0.3.1 \
			github.com/labstack/gommon/color@v0.4.0 \
			github.com/prometheus/common/config@v0.46.0 \
			k8s.io/apiserver/pkg/storage/etcd3@v0.29.0 \
			k8s.io/apiserver/pkg/storage/storagebackend/factory@v0.29.0 \
			k8s.io/client-go/tools/clientcmd@v0.29.0; do \
			$(GO_ENV_COMMON) $(GO_BIN) get $$$$p; \
		done; \
	)
	# godeltaprof@v0.1.6/internal/pprof/delta_mutex.go:30:20: undefined: runtime_cyclesPerSecond
	# godeltaprof@v0.1.6/internal/pprof/proto.go:320:8: undefined: runtime_expandFinalInlineFrame
	( cd $(PKG_BUILD_DIR); \
		$(GO_ENV_COMMON) $(GO_BIN) get github.com/grafana/pyroscope-go/godeltaprof@v0.1.7; \
	)
	# generate code from .cue files
	( cd $(PKG_BUILD_DIR); \
		for t in \
			./pkg/plugins/plugindef \
			./kinds/gen.go \
			./public/app/plugins/gen.go \
			./pkg/kindsysreport/codegen/report.go; do \
			$(GO_BIN_GENERATE) $$$$t; \
		done; \
	)
	# generate go files
	( cd $(PKG_BUILD_DIR); \
		$(GO_ENV_COMMON) $(PKG_BUILD_DIR)/wire-host gen -tags "oss" ./pkg/server; \
	)
endef

Hooks/Compile/Pre += Build/Deps

define Build/Web
	# build frontend
	# FIXME JavaScript heap out of memory
	( cd $(PKG_BUILD_DIR); \
		NODE_OPTIONS=--max_old_space_size=8000 yarn run build; \
	)
	( cd $(PKG_BUILD_DIR)/plugins-bundled/internal/input-datasource; \
		yarn run build; \
	)
endef

Hooks/Compile/Post += Build/Web

define Package/grafana/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/grafana{,-cli,-server} $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/grafana
	$(CP) $(PKG_BUILD_DIR)/conf/sample.ini $(1)/opt/etc/grafana/grafana.ini
	$(CP) $(PKG_BUILD_DIR)/conf/ldap.toml $(1)/opt/etc/grafana
	$(CP) $(PKG_BUILD_DIR)/conf/provisioning $(1)/opt/etc/grafana
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S80grafana $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/share/grafana
	$(CP) $(PKG_BUILD_DIR)/{conf,public} $(1)/opt/share/grafana
	$(INSTALL_DIR) $(1)/opt/share/grafana/plugins-bundled/internal/input-datasource
	$(CP) $(PKG_BUILD_DIR)/plugins-bundled/internal/input-datasource/dist/* \
		$(1)/opt/share/grafana/plugins-bundled/internal/input-datasource
	# XXX remove *.map
	$(FIND) $(1)/opt/share/grafana -type f -a -iname "*.map" -delete
	$(INSTALL_DIR) $(1)/opt/var/lib/grafana/plugins
	$(INSTALL_DIR) $(1)/opt/var/log/grafana
endef

Package/grafana_nohf/install=$(Package/grafana/install)

$(eval $(call BuildPackage,grafana))
$(eval $(call BuildPackage,grafana_nohf))

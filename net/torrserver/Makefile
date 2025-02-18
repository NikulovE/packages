include $(TOPDIR)/rules.mk

PKG_NAME:=torrserver
PKG_VERSION:=121
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/YouROK/TorrServer.git
PKG_SOURCE_VERSION:=97238bdcdc2b2ca8a56f60b2fbc4adec1114fc70
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_LICENSE:=GPL-3.0-or-later
PKG_MAINTAINER:=Evgeny Nikulov <nikulov@live.ru>

# Для сборки веб-части требуются golang, node и yarn
PKG_BUILD_DEPENDS:=golang/host node/host node-yarn/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/torrserver
    SUBMENU:=BitTorrent
	SECTION:=net
	CATEGORY:=Network
	TITLE:=TorrServer - media streaming server for torrents
	URL:=https://github.com/YouROK/TorrServer
	DEPENDS:=$(GO_ARCH_DEPENDS) +procd
endef

define Package/torrserver/description
TorrServer is a media streaming server for torrents
endef

define Build/Compile
	( \
		pushd $(PKG_BUILD_DIR) ; \
		export NODE_OPTIONS=--openssl-legacy-provider ; \
		go run gen_web.go ; \
		cd server ; \
		go mod tidy ; \
		go clean -i -r -cache ; \
		GOOS=linux GOARCH=$(GO_ARCH) CGO_ENABLED=0 go build -ldflags="-w -s" -o torrserver ./cmd ; \
	)
endef

define Package/torrserver/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/server/torrserver $(1)/usr/bin/torrserver
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/torrserver.init $(1)/etc/init.d/torrserver
endef

$(eval $(call GoBinPackage,torrserver))
$(eval $(call BuildPackage,torrserver))
#
# Makefile для сборки torrserver для OpenWrt (aarch64)
#

include $(TOPDIR)/rules.mk

PKG_NAME:=torrserver
PKG_VERSION:=134
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/YouROK/TorrServer.git
PKG_SOURCE_VERSION:=f76add1585131f21b2aa135d610acecc65c1e949
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

PKG_LICENSE:=GPL-3.0-or-later
PKG_MAINTAINER:=Evgeny Nikulov <nikulov@live.ru>

# Для сборки веб-части требуются golang, node и yarn
PKG_BUILD_DEPENDS:=golang/host node/host node-yarn/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG_LDFLAGS_X:= -w -s

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/torrserver
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
		$(call GoPackage/Build/Compile,-o torrserver ./cmd) ; \
	)
endef

define Package/torrserver/install
	$(call GoPackage/Package/Install/Bin,$(1))
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/torrserver.init $(1)/etc/init.d/torrserver
endef

$(eval $(call GoBinPackage,torrserver))
$(eval $(call BuildPackage,torrserver))
#
# Copyright (C) 2014 Arduino LLC
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=cylon
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=0.24.0
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/hybridgroup/cylon-firmata.git
PKG_SOURCE_VERSION:=a930f8446f23ec2cb28aadeff54b79ab7704e3a0
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_SOURCE_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_SOURCE_VERSION).tar.gz
PKG_MIRROR_HASH:=dceb75539d32f402db0a5f68f2c7e2b52e5547a5ac2dec875d34fd3cc95cce00

PKG_BUILD_DEPENDS:=node/host
PKG_NODE_VERSION:=`$(STAGING_DIR_HOSTPKG)/bin/node --version`

PKG_MAINTAINER:=John Crispin <blogic@openwrt.org>
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/node-cylon/default
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=CylonJS - $(1)
  URL:=https://www.npmjs.org/package/cylon
  DEPENDS:=+node +node-npm $(2)
endef

define Package/node-cylon
  $(call Package/node-cylon/default,Core)
endef

define Package/node-cylon-i2c
  $(call Package/node-cylon/default,I2C,+node-cylon)
endef

define Package/node-cylon-gpio
  $(call Package/node-cylon/default,GPIO,+node-cylon)
endef

define Package/node-cylon-firmata
  $(call Package/node-cylon/default,Firmata,+node-cylon-gpio +node-cylon-i2c +node-arduino-firmata)
endef

define Package/node-cylon/description
	JavaScript Robotics, By Your Command Next generation robotics framework with support for 36 different platforms Get Started
endef

define Build/Prepare
	/bin/tar xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip-components 1
	$(Build/Patch)
endef

EXTRA_LDFLAGS="-L$(TOOLCHAIN_DIR)/lib/ -Wl,-rpath-link $(TOOLCHAIN_DIR)/lib/" \

define Build/Compile
	cd $(PKG_BUILD_DIR) ; \
	$(MAKE_FLAGS) \
	npm_config_arch=$(CONFIG_ARCH) \
	npm_config_nodedir=$(BUILD_DIR)/node-$(PKG_NODE_VERSION)/ \
	npm_config_cache=$(BUILD_DIR)/node-$(PKG_NODE_VERSION)/npm-cache \
	PREFIX="$(PKG_INSTALL_DIR)/usr/" \
	npm install -g `npm pack $(PKG_BUILD_DIR) | tail -n 1`
endef

define Package/node-cylon/install
	mkdir -p $(1)/usr/lib/node/cylon
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/cylon-firmata/node_modules/cylon/* $(1)/usr/lib/node/cylon/
endef

define Package/node-cylon-i2c/install
	mkdir -p $(1)/usr/lib/node/cylon-i2c
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/cylon-firmata/node_modules/cylon-i2c/* $(1)/usr/lib/node/cylon-i2c/
endef

define Package/node-cylon-gpio/install
	mkdir -p $(1)/usr/lib/node/cylon-gpio
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/cylon-firmata/node_modules/cylon-gpio/* $(1)/usr/lib/node/cylon-gpio/
endef

define Package/node-cylon-firmata/install
	mkdir -p $(1)/usr/lib/node/cylon-firmata
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/cylon-firmata/{index.js,lib,LICENSE,package.json,README.md,RELEASES.md,spec} $(1)/usr/lib/node/cylon-firmata/
	# Strip PKG_BUILD_DIR from useless metadata inserted by npm install
	# https://github.com/npm/npm/issues/10393
	# https://github.com/npm/npm/issues/12110
	find $(1)/usr/lib/node -name package.json -exec sed -i -e 's,$(PKG_BUILD_DIR),,g' {} +
endef

$(eval $(call BuildPackage,node-cylon))
$(eval $(call BuildPackage,node-cylon-i2c))
$(eval $(call BuildPackage,node-cylon-gpio))
$(eval $(call BuildPackage,node-cylon-firmata))

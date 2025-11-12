---
title: "OpenWrt Package Creation: A Quick Guide"
seoTitle: "Create OpenWrt Packages: Fast and Easy Guide"
seoDescription: "Learn to create custom C/C++ packages for OpenWrt on the HLK-7628N board with this quick and easy guide"
datePublished: Wed Nov 12 2025 14:05:55 GMT+0000 (Coordinated Universal Time)
cuid: cmhw2p1n8000002job8ud1tjy
slug: openwrt-package-creation-a-quick-guide
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/U52NDoOl6gg/upload/cd937ba9a6f1ba71077049452772b1ff.jpeg
tags: linux, openwrt, build-system

---

This post outlines the process of building and deploying a custom C/C++ package on an OpenWrt-enabled HLK-7628N development board. To set up a build environment quickly, you just need to download the openwrt-sdk for your hardware platform. In this case, I'm using the HLK-7628N dev board, whose [sdk](https://downloads.openwrt.org/releases/24.10.0/targets/ramips/mt76x8/openwrt-sdk-24.10.0-ramips-mt76x8_gcc-13.3.0_musl.Linux-x86_64.tar.zst) and [firmware](https://downloads.openwrt.org/releases/24.10.0/targets/ramips/mt76x8/openwrt-24.10.0-ramips-mt76x8-hilink_hlk-7628n-squashfs-sysupgrade.bin) can be downloaded from the OpenWrt website.

#### 1\. Download SDK and Firmware

First, get the necessary files.

* Go to the [OpenWrt Downloads](https://downloads.openwrt.org/) page.
    
* Navigate to your device's target (e.g., `targets/ramips/mt76x8`).
    
* Download the **SDK** for your specific release (e.g., `openwrt-sdk-*-ramips-mt76x8_gcc-*-musl.Linux-x86_64.tar.zst`).
    
* Download the **firmware image** (`squashfs-sysupgrade.bin`) for your board, if you need to flash it.
    

#### 2\. Extract the SDK

Extract the downloaded SDK archive to your desired working directory.

```bash
tar --zstd -xfv openwrt-sdk-*-mt7628_gcc-*-musl.Linux-x86_64.tar.xz
cd openwrt-sdk-* # Navigate into the extracted SDK directory
```

#### 3\. Create Package Folder and Add to Feeds

Create your package directory and integrate it with the SDK's build system.

```bash
# Create your package directory
mkdir -p /path/to/sdk/my_custom_feed
src-link my_custom_feed /path/to/sdk/my_custom_feed
# Update and install feeds (ensure you're in the SDK root)
./scripts/feeds update -a
./scripts/feeds install -a
```

#### 4\. Write the Package Makefile and Source Code

Place your C++ source code and the OpenWrt Makefile in the `my_custom_feed/my-first-app` directory.

`my_custom_feed/my-first-app/main.cpp`

```cpp
#include <iostream>

int main(int argc, char* argv[]) {
    std::cout << "Hello from the HLK-7628!" << std::endl;
    return 0;
}
```

`my_custom_feed/my-first-app/CMakeLists.txt`

```plaintext
cmake_minimum_required(VERSION 3.5)
project(my-first-app CXX)

add_executable(my-first-app main.cpp)
install(TARGETS my-first-app  DESTINATION /usr/bin)
```

`my_custom_feed/my-first-app/Makefile`

```makefile
include $(TOPDIR)/rules.mk

PKG_NAME:=my-first-app
PKG_VERSION:=1.0
PKG_RELEASE:=1


SOURCE_DIR:=$(TOPDIR)/my_custom_feed/my-first-app
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
# You can include different mk file for different build systems...
include $(INCLUDE_DIR)/cmake.mk

define Package/my-first-app
       SECTION:=utils        
       CATEGORY:=Utilities
       TITLE:=My First Custom Application
       DEPENDS:=+libstdcpp # or other libs
endef

define Build/Prepare
	     mkdir -p $(PKG_BUILD_DIR)
        cp -r $(SOURCE_DIR)/* $(PKG_BUILD_DIR)/
endef


define Package/my-first-app/install
  $(INSTALL_DIR) $(1)/usr/bin
  $(INSTALL_BIN) $(PKG_BUILD_DIR)/my-first-app $(1)/usr/bin/
endef

$(eval $(call BuildPackage,my-first-app))
```

#### 5\. Select the Package and Build

Configure the SDK to build your package and then compile it.

```bash
# From the SDK root directory:
make menuconfig
```

* Navigate to `Utilities`.
    
* Find `my-first-app` and select it by pressing `M` (to build as a module/package).
    
* Save and exit.
    

```bash
# Build the package (from the SDK root directory)
./scripts/feeds update my_custom_feed
./scripts/feeds install my-first-app
make package/my-first-app/compile V=s
```

Your `.ipk` package will be in `bin/packages/mipsel_24kc/my_custom_feed/`.

#### 6\. Push to Device, Install, and Test

Transfer the package to your HLK-7628, install it, and verify functionality.

```bash
# Copy the .ipk to your device (replace with actual IP and filename)
scp bin/packages/mipsel_24kc/my_custom_feed/my-first-app_1.0-1_mipsel_24kc.ipk root@<router_ip>:/tmp/

# SSH into your device
ssh root@<router_ip>

# Install the package
opkg install /tmp/my-first-app_1.0-1_mipsel_24kc.ipk

# Run your application
my-first-app
```

You should see: `Hello from the HLK-7628!`

Now that you've seen how to build and deploy your own package, the possibilities are endless. What custom functionality will you add to your HLK-7628?

Try this tutorial for yourself and share your experience in the comments below. I'd love to hear about the custom tools or features you're creating for your own router!
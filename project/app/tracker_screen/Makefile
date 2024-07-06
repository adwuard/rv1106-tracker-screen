
ifeq ($(APP_PARAM), )
    APP_PARAM:=../Makefile.param
    include $(APP_PARAM)
endif

export LC_ALL=C
SHELL:=/bin/bash

CURRENT_DIR := $(shell pwd)

PKG_NAME := tracker-screen
PKG_BIN ?= out
PKG_BUILD ?= build


ifneq ($(RK_BUILD_VERSION_TYPE), DEBUG)
PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=Release
else
PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=Debug
endif

RK_APP_CFLAGS = -I ./include

RK_APP_LDFLAGS = -L $(RK_APP_MEDIA_LIBS_PATH) -L$(RK_APP_OUTPUT)/lib

RK_APP_OPTS += -Wl,-rpath-link,$(RK_APP_MEDIA_LIBS_PATH):$(RK_APP_PATH_LIB_INCLUDE)/root/usr/lib:$(RK_APP_OUTPUT)/lib
PKG_CONF_OPTS += -DCMAKE_C_FLAGS="$(RK_APP_CFLAGS) $(RK_APP_LDFLAGS) $(RK_APP_OPTS)" \
				-DCMAKE_CXX_FLAGS="$(RK_APP_CFLAGS) $(RK_APP_LDFLAGS) $(RK_APP_OPTS)"

PKG_CONF_OPTS += -DRKIPC_CROSS_COMPILE="$(RK_APP_CROSS)"

PKG_TOOLCHAIN_SYSROOT := $(shell $(RK_APP_CROSS)-gcc -print-sysroot)
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH="$(CURRENT_DIR)/$(PKG_BIN);$(RK_APP_OUTPUT);$(PKG_TOOLCHAIN_SYSROOT)"
PKG_CONF_OPTS += -DCMAKE_SYSROOT="$(PKG_TOOLCHAIN_SYSROOT)"
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY
PKG_CONF_OPTS += -DCMAKE_NO_SYSTEM_FROM_IMPORTED=ON
PKG_CONF_OPTS += -DCMAKE_SYSTEM_NAME=Linux


SDL_PKG_CONF_OPTS = PKG_CONF_OPTS
SDL_PKG_CONF_OPTS += -DSDL_VIDEO=OFF \
				-DSDL_DIRECTFB=ON \
				-DSDL_ALSA=ON \
				-DSDL_ARMNEON=ON \
				-DSDL_ALSA_SHARED=ON \
				-DSDL_IBUS=OFF \
				-DSDL_PULSEAUDIO=OFF \
				-DSDL_PULSEAUDIO_SHARED=OFF \
				-DSDL_WAYLAND=OFF \
				-DSDL_WAYLAND_SHARED=OFF \
				-DSDL_OPENGL=OFF \
				-DSDL_OPENGLES=OFF \
				-DSDL_OSS=OFF \
				-DSDL_X11=OFF \
				-DSDL_X11_SHARED=OFF \
				-DSDL_ESD=OFF \
				-DSDL_JACK=OFF \
				-DSDL_JACK_SHARED=OFF \
				-DSDL_NAS=OFF \
				-DSDL_NAS_SHARED=OFF



ifeq ($(PKG_BIN),)
$(error ### $(CURRENT_DIR): PKG_BIN is NULL, Please Check !!!)
endif

ifeq ($(EDSLAB_TRACKER_SCREEN_ENABLED),y)
PKG_TARGET := libsdl2-build libserialport-build m8c-build
# PKG_TARGET :=  libserialport-build m8c-build

all: $(PKG_TARGET)
	@echo "build $(PKG_NAME) done"
endif



libsdl2-build:
	@echo "RK_APP_CHIP is $(RK_APP_CHIP)"
	@echo "RK_APP_TYPE is $(RK_APP_TYPE)"
	
	mkdir -p $(PKG_BIN);
	mkdir -p $(PKG_BUILD);
	pushd $(PKG_BUILD)/; \
		rm -rf CMakeCache.txt; \
		cmake $(CURRENT_DIR)/SDL/ \
			-DCMAKE_C_COMPILER=$(RK_APP_CROSS)-gcc \
			-DCMAKE_CXX_COMPILER=$(RK_APP_CROSS)-g++ \
			-DCMAKE_INSTALL_PREFIX="$(CURRENT_DIR)/$(PKG_BIN)" \
			$(SDL_PKG_CONF_OPTS) ;\
			make -j$(RK_APP_JOBS) || exit -1; \
			make install; \
	popd;
	$(call MAROC_COPY_PKG_TO_APP_OUTPUT, $(RK_APP_OUTPUT), $(PKG_BIN))
	@echo "=====> build libsdl2 done"


libserialport-build:
	@echo "RK_APP_CHIP is $(RK_APP_CHIP)"
	@echo "RK_APP_TYPE is $(RK_APP_TYPE)"

	mkdir -p $(PKG_BIN);
	mkdir -p $(PKG_BUILD);

	pushd $(PKG_BUILD)/; \
		rm -rf CMakeCache.txt; \
		cmake $(CURRENT_DIR)/libserialport-cmake/ \
			-DCMAKE_C_COMPILER=$(RK_APP_CROSS)-gcc \
			-DCMAKE_CXX_COMPILER=$(RK_APP_CROSS)-g++ \
			-DCMAKE_INSTALL_PREFIX="$(CURRENT_DIR)/$(PKG_BIN)" \
			$(PKG_CONF_OPTS) ;\
			make -j$(RK_APP_JOBS) || exit -1; \
			make install; \
	popd;
	$(call MAROC_COPY_PKG_TO_APP_OUTPUT, $(RK_APP_OUTPUT), $(PKG_BIN))
	@echo "=====> build libserialport-build done"

m8c-build:
	@echo "=====> build m8c done"


# @test ! -d $(RK_APP_COMPONENT)/third_libs || make -C $(RK_APP_COMPONENT)/third_libs



clean:
	@rm -rf $(PKG_BIN) $(PKG_BUILD)

distclean: clean

info:
ifeq ($(EDSLAB_TRACKER_SCREEN_ENABLED),y)
	@echo -e "$(C_YELLOW)-------------------------------------------------------------------------$(C_NORMAL)"
	@echo -e "$(C_YELLOW)-------------------------------------------------------------------------$(C_NORMAL)"
endif

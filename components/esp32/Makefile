#
# Component Makefile
#
# This Makefile should, at the very least, just include $(IDF_PATH)/make/component.mk. By default, 
# this will take the sources in this directory, compile them and link them into 
# lib(subdirectory_name).a in the build directory. This behaviour is entirely configurable,
# please read the esp-idf build system document if you need to do this.
#
-include $(PROJECT_PATH)/build/include/config/auto.conf

LIBS := crypto core net80211 phy rtc pp wpa wps

ifeq ($(CONFIG_MEMMAP_BT),y)
    ifeq ($(CONFIG_MEMMAP_TRACEMEM),y)
        LINKER_SCRIPTS = -T esp32.bt.trace.ld
    else
        LINKER_SCRIPTS = -T esp32.bt.ld
    endif
else
    ifeq ($(CONFIG_MEMMAP_TRACEMEM),y)
        LINKER_SCRIPTS = -T esp32.trace.ld
    else
        LINKER_SCRIPTS = -T esp32.ld
    endif
endif

LINKER_SCRIPTS += -T esp32.common.ld -T esp32.rom.ld

COMPONENT_ADD_LDFLAGS := -lesp32 \
                           $(abspath libhal.a) \
                           -L$(abspath lib) \
                           $(addprefix -l,$(LIBS)) \
                          -L $(abspath ld) \
                          $(LINKER_SCRIPTS)

include $(IDF_PATH)/make/component.mk

ALL_LIB_FILES := $(patsubst %,$(COMPONENT_PATH)/lib/lib%.a,$(LIBS))

# The binary libraries are in a git submodule, so this target will
# be invoked if any modules are missing (probably because
# git submodule update --init needs to be run).
$(ALL_LIB_FILES):
	$(Q) [ -d ${IDF_PATH}/.git ] || ( @echo "ERROR: Missing libraries in esp32 component. esp-idf must be cloned from git to work."; exit 1 )
	$(Q) [ -x $(which git) ] || ( @echo "ERROR: Missing libraries in esp32 component. Need to run 'git submodule update --init' in esp-idf root directory."; exit 1 )
	@echo "Warning: Missing libraries in components/esp32/lib/ submodule. Going to try running 'git submodule update --init' in esp-idf root directory..."
	cd ${IDF_PATH} && git submodule update --init

# adding $(ALL_LIB_FILES) as a build dependency here is a hack to make
# sure they get evaluated. Once TW6630 lands and we have library file
# dependencies available at the project level, we can probably lose
# this.
build: $(ALL_LIB_FILES)

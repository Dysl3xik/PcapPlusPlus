ifeq ($(wildcard ../../mk/platform.mk),)
  $(error platform.mk not found! Please run configure script first)
endif

include ../../mk/platform.mk

ifeq ($(wildcard ../../mk/PcapPlusPlus.mk),)
  $(error PcapPlusPlus.mk not found! Please run configure script first)
endif

include ../../mk/PcapPlusPlus.mk

ifeq ($(wildcard ../../3rdParty/MemPlumber/Makefile.defs),)
  $(error MemPlumber/Makefile.defs not found!)
endif

include ../../3rdParty/MemPlumber/Makefile.defs

SOURCES := $(wildcard *.cpp)
OBJS_FILENAMES := $(patsubst %.cpp,Obj/%.o,$(SOURCES))

ENDIAN_PORTABLE_INCLUDE := $(PCAPPLUSPLUS_HOME)/3rdParty/EndianPortable/include

PCPP_TEST_FRAMEWORK := ../PcppTestFramework

PCAPPP_INCLUDES += -I$(MEMPLUMBER_INCLUDES) -I$(ENDIAN_PORTABLE_INCLUDE) -I$(PCPP_TEST_FRAMEWORK)

ifdef WIN32
DEPS := -DHAVE_STRUCT_TIMESPEC
endif
ifdef LINUX
DEPS := -DLINUX
endif
ifdef PF_RING_HOME
DEPS += -DUSE_PF_RING
endif
ifdef USE_DPDK
DEPS += -DUSE_DPDK
endif
ifdef MAC_OS_X
DEPS := -DMAC_OS_X
endif
ifdef HAS_SET_DIRECTION_ENABLED
DEPS += -DHAS_SET_DIRECTION_ENABLED
endif

COMMON_DEBUG_LIB_DIR := -L../../Common++/Lib/Debug

Obj/%.o: %.cpp
	@echo 'Building file: $<'
	@$(CXX) $(DEPS) $(PCAPPP_INCLUDES) $(PCAPPP_BUILD_FLAGS) -O2 -g -Wall -c -fmessage-length=0 -MMD -MP -MF"$(@:Obj/%.o=Obj/%.d)" -MT"$(@:Obj/%.o=Obj/%.d)" -o "$@" "$<"


UNAME := $(shell uname)
CUR_TARGET := $(notdir $(shell pwd))

.SILENT:

all: dependents Pcap++Test

start:
	@echo '==> Building target: $(CUR_TARGET)'

create-directories:
	@$(MKDIR) -p Obj
	@$(MKDIR) -p Bin

dependents:
	cd $(PCAPPLUSPLUS_HOME) && $(MAKE) libs
	
Pcap++Test: start create-directories $(OBJS_FILENAMES)
	@cd $(MEMPLUMBER_HOME) && $(MAKE) memplumber_sources
	@$(CXX) $(COMMON_DEBUG_LIB_DIR) $(PCAPPP_LIBS_DIR) $(PCAPPP_BUILD_FLAGS) -o "./Bin/Pcap++Test$(BIN_EXT)" $(OBJS_FILENAMES) $(MEMPLUMBER_OBJS_FILENAMES) $(PCAPPP_LIBS)
	@$(PCAPPP_POST_BUILD)
	@echo 'Finished successfully building: $(CUR_TARGET)'
	@echo ' '

clean:
	@cd $(MEMPLUMBER_HOME) && $(MAKE) clean
	@$(RM) -rf ./Obj/*
	@$(RM) -rf ./Bin/*
	@echo 'Clean finished: $(CUR_TARGET)'

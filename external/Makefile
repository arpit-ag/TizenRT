############################################################################
# external/Makefile
#
#   Copyright (C) 2007, 2008, 2011-2015 Gregory Nutt. All rights reserved.
#   Author: Gregory Nutt <gnutt@nuttx.org>
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in
#    the documentation and/or other materials provided with the
#    distribution.
# 3. Neither the name NuttX nor the names of its contributors may be
#    used to endorse or promote products derived from this software
#    without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
# COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
# BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
# OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
# AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
############################################################################


#EXTDIR = ${shell pwd}
-include $(TOPDIR)/Make.defs

DELIM ?= $(strip /)

DEPPATH = --dep-path .
ASRCS =
CSRCS =
VPATH = .


ifeq ($(CONFIG_WICED),y)
include WICED$(DELIM)Make.defs
endif

ifeq ($(CONFIG_DM),y)
ifeq ($(CONFIG_LWM2M_WAKAAMA),y)
include wakaama/core/Make.defs
include wakaama/core/er-coap-13/Make.defs
include wakaama/examples/shared/Make.defs
endif
ifeq ($(CONFIG_LWM2M_CLIENT_MODE),y)
include wakaama/examples/client/Make.defs
endif
endif

ifeq ($(CONFIG_ARCH_CHIP_BCM4390X),y)
CFLAGS+=-I$(TOPDIR)/../external/WICED/bcm4390x/include
endif
ifeq ($(CONFIG_DM),y)
CFLAGS+=-I$(TOPDIR)/../framework/include/dm
endif

AOBJS = $(ASRCS:.S=$(OBJEXT))
COBJS = $(CSRCS:.c=$(OBJEXT))

SRCS = $(ASRCS) $(CSRCS)
OBJS = $(AOBJS) $(COBJS)

BIN	= libexternal$(LIBEXT)

all:	$(BIN)

$(AOBJS): %$(OBJEXT): %.S
	$(call ASSEMBLE, $<, $@)

$(COBJS): %$(OBJEXT): %.c
	$(call COMPILE, $<, $@)

$(BIN): $(OBJS)
ifeq ($(CONFIG_IOTIVITY_SCONS_BUILD),y)
	$(Q) echo "Launching IoTivity Build"
	$(Q) TOPDIR="$(TOPDIR)" $(TOPDIR)/../external/iotivity/build_iotivity.sh
endif
	$(call ARCHIVE, $@, $(OBJS))

.depend: Makefile $(SRCS)
	$(Q) $(MKDEP) $(DEPPATH) "$(CC)" -- $(CFLAGS) -- $(SRCS) >Make.dep
	$(Q) touch $@

depend: .depend

custom_clean:
	$(foreach OBJFILE, $(OBJS), rm -f $(OBJFILE))

clean: custom_clean
ifeq ($(CONFIG_IOTIVITY_SCONS_BUILD),y)
	$(Q) echo "Cleaning IoTivity Build"
	$(Q) TOPDIR="$(TOPDIR)" $(TOPDIR)/../external/iotivity/clean_iotivity.sh
	$(Q) rm -rf $(TOPDIR)/../external/iotivity/iotivity_$(CONFIG_IOTIVITY_RELEASE_VERSION)/out
	$(Q) rm -rf $(TOPDIR)/../external/iotivity/iotivity_$(CONFIG_IOTIVITY_RELEASE_VERSION)/.sconsign.dblite
	$(Q) rm -rf $(TOPDIR)/../external/iotivity/iotivity_$(CONFIG_IOTIVITY_RELEASE_VERSION)/.sconf_temp
	$(call DELFILE, $(TOPDIR)/../external/iotivity/iotivity_$(CONFIG_IOTIVITY_RELEASE_VERSION)/config.log)
endif
	$(call DELFILE, $(BIN))
	$(call CLEAN)

distclean: clean
	$(call DELFILE, Make.dep)
	$(call DELFILE, .depend)

-include Make.dep

#
# Copyright  1990-2009 Sun Microsystems, Inc. All Rights Reserved.
# DO NOT ALTER OR REMOVE COPYRIGHT NOTICES OR THIS FILE HEADER
# 
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License version
# 2 only, as published by the Free Software Foundation.
# 
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License version 2 for more details (a copy is
# included at /legal/license.txt).
# 
# You should have received a copy of the GNU General Public License
# version 2 along with this work; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA
# 02110-1301 USA
# 
# Please contact Sun Microsystems, Inc., 4150 Network Circle, Santa
# Clara, CA 95054 or visit www.sun.com if you need additional
# information or have any questions.
#
######################################################################
#
# Makefile for building and packaging.
#
######################################################################

# Workspace directory
export PCSL_DIR		= $(CURDIR)

# Project speficic definitions
ifndef PROJECT_PCSL_DIR
PROJECT_PCSL_DIR=$(PCSL_DIR)
endif

# PCSL configuration files may add targets like verify
# So, to preserve all: as default target, it also defined here
all:

# include top.gmk for various directory and module definitions
ifdef PCSL_PLATFORM
include makefiles/top.gmk
endif

dep_output_inc_dir:=$(OUTPUT_INC_DIR)/.dep_output_inc_dir
dep_output_obj_dir:=$(OUTPUT_OBJ_DIR)/.dep_output_obj_dir
dep_output_lib_dir:=$(OUTPUT_LIB_DIR)/.dep_output_lib_dir
dep_output_bin_dir:=$(OUTPUT_BIN_DIR)/.dep_output_bin_dir
dep_output_gen_dir:=$(OUTPUT_GEN_DIR)/.dep_output_gen_dir

$(dep_output_inc_dir) $(dep_output_obj_dir) $(dep_output_lib_dir) \
$(dep_output_bin_dir) $(dep_output_gen_dir):
	@mkdir -p $(dir $@); touch $@

all: verify \
$(TYPES_DIR) $(PRINT_DIR) $(MEMORY_DIR) $(FILE_DIR) $(NETWORK_DIR) $(STRING_DIR) \
$(dep_output_inc_dir) $(dep_output_obj_dir) $(dep_output_lib_dir) generate_config
	@echo "building all modules..."
	cd ${TYPES_DIR}; $(MAKE) $@
	cd ${PRINT_DIR}; $(MAKE) $@
	cd ${MEMORY_DIR}; $(MAKE) $@
	cd ${STRING_DIR}; $(MAKE) $@
	cd ${FILE_DIR}; $(MAKE) $@
	cd ${NETWORK_DIR}; $(MAKE) $@
	cd ${ESCFILENAMES_DIR}; $(MAKE) $@

NUM_JOBS ?= 2
parallel_all:
	$(MAKE) -j $(NUM_JOBS) all

include makefiles/verify.gmk

# doc stuff. The 'doc' target is defined in Docs.gmk.
# Just have to define DOXYGEN_INPUT_LIST and force
# rebuild

FRC_DOC_REBUILD = force_doc_rebuild
DOXYGEN_INPUT_LIST += \
    $(TYPES_DIR) \
    $(TYPES_MD_DIR) \
    $(NETWORK_DIR) \
    $(NETWORK_DIR)/serial \
    $(NETWORK_DIR)/bsd \
    $(STRING_DIR) \
    $(MEMORY_DIR) \
    $(MEMORY_PORT_DIR) \
    $(FILE_DIR) \
    $(PRINT_DIR)

include $(PCSL_DIR)/makefiles/share/Docs.gmk

# Source bundle creation target
#

include $(PCSL_DIR)/makefiles/share/release.gmk

# donuts
#

donuts: verify $(DONUTS_DIR) $(dep_output_inc_dir) $(dep_output_obj_dir) \
$(dep_output_lib_dir) $(dep_output_bin_dir) $(dep_output_gen_dir)
	cd $(DONUTS_DIR) ; $(MAKE) all

PCSL_CONFIG_FILENAME = $(OUTPUT_INC_DIR)/pcsl_config.h

generate_config: $(dep_output_inc_dir)
	@echo "generating config file..."
	@echo '/* This file is auto generated by make file. Do not edit. */' > $(PCSL_CONFIG_FILENAME)
	@echo '#ifndef _PCSL_CONFIG_H_' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define _PCSL_CONFIG_H_' >> $(PCSL_CONFIG_FILENAME)
	@echo  >> $(PCSL_CONFIG_FILENAME)
ifeq ($(USE_DEBUG), true)
	@echo '/* Build PCSL library with DEBUG support */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define PCSL_DEBUG' >> $(PCSL_CONFIG_FILENAME)
endif
ifeq ($(USE_DATAGRAM), true)
	@echo '/* Enable datagram API */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define PCSL_DATAGRAM' >> $(PCSL_CONFIG_FILENAME)
endif
ifeq ($(USE_SERVER_SOCKET), true)
	@echo '/* Enable server socket API */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define PCSL_SERVER_SOCKET' >> $(PCSL_CONFIG_FILENAME)
endif
	@echo '/* File system module */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define FILE_MODULE $(FILE_MODULE)' >> $(PCSL_CONFIG_FILENAME)
	@echo '/* Network module */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define NETWORK_MODULE $(NETWORK_MODULE)' >> $(PCSL_CONFIG_FILENAME)
	@echo '/* Memory module */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define MEMORY_MODULE $(MEMORY_MODULE)' >> $(PCSL_CONFIG_FILENAME)
	@echo '/* Print module */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define PRINT_MODULE $(PRINT_MODULE)' >> $(PCSL_CONFIG_FILENAME)
	@echo '/* String module */' >> $(PCSL_CONFIG_FILENAME)
	@echo '#define STRING_MODULE $(STRING_MODULE)' >> $(PCSL_CONFIG_FILENAME)
	@echo  >> $(PCSL_CONFIG_FILENAME)
	@echo '#endif /*_PCSL_CONFIG_H_*/' >> $(PCSL_CONFIG_FILENAME)

clean: verify_for_clean
	rm -rf $(PCSL_OUTPUT_DIR)/$(PCSL_OS)_$(PCSL_CPU)
	rm -rf $(PCSL_OUTPUT_DIR)/tools
	rm -rf $(DOC_DIR)
	-rmdir $(PCSL_OUTPUT_DIR)

.PHONY: all clean donuts doc verify

CXX_FLAGS := -fPIC -std=c++11 -O3 -Wall
BUILD_DIR := build
BDWGC_DIR := $(BUILD_DIR)/bdwgc
STARK_DIR := $(BUILD_DIR)/stark
RUNTIME_SRC_DIR := $(STARK_DIR)/runtime
DOCKCROSS_CMD := dockcross
ARCHIVE := archive.zip

ifeq ($(STARK_BRANCH),)
STARK_BRANCH := develop
endif

all: arm-unknown-linux-gnueabihf arm-unknown-linux-musleabihf archive

# Compile target function
# $(1) is the dockcross toolchain name (example: linux-armv6). List available here: https://github.com/dockcross/dockcross
# $(2) is the target triple used for compilation
define compile_target
	@echo "Compiling $(2) with $(1)"
	@mkdir -p $(BUILD_DIR)/$(2)
    @docker run --rm dockcross/$(1) > $(BUILD_DIR)/$(DOCKCROSS_CMD)
	@chmod +x $(BUILD_DIR)/$(DOCKCROSS_CMD)

	@cd $(BDWGC_DIR) && ../$(DOCKCROSS_CMD) cmake -DBUILD_SHARED_LIBS=OFF -Bbuild-$(1) -H. -GNinja
	@cd $(BDWGC_DIR) && ../$(DOCKCROSS_CMD) ninja -Cbuild-$(1)

	$(eval LIB_DIR := $(BUILD_DIR)/$(2))
	@mkdir -p $(LIB_DIR)/libstark
	
	@./$(BUILD_DIR)/$(DOCKCROSS_CMD) bash -c '$$CC $(CXX_FLAGS) -I $(BDWGC_DIR)/include -c $(RUNTIME_SRC_DIR)/IO.cpp -o $(LIB_DIR)/libstark/io.o'
	@./$(BUILD_DIR)/$(DOCKCROSS_CMD) bash -c '$$CC $(CXX_FLAGS) -I $(BDWGC_DIR)/include -c $(RUNTIME_SRC_DIR)/Conversion.cpp -o $(LIB_DIR)/libstark/conversion.o'
	@./$(BUILD_DIR)/$(DOCKCROSS_CMD) bash -c '$$CC $(CXX_FLAGS) -I $(BDWGC_DIR)/include -c $(RUNTIME_SRC_DIR)/Memory.cpp -o $(LIB_DIR)/libstark/memory.o'
	
	@cp $(BDWGC_DIR)/build-$(1)/libgc.a $(LIB_DIR)/libstark
	@cd $(LIB_DIR)/libstark && ../../$(DOCKCROSS_CMD) ar -x libgc.a
	@rm $(LIB_DIR)/libstark/libgc.a
	@rm -rf $(LIB_DIR)/libstark.a
	@./$(BUILD_DIR)/$(DOCKCROSS_CMD) ar -rcs $(LIB_DIR)/libstark.a $(LIB_DIR)/libstark/*.o
	@rm -rf $(LIB_DIR)/libstark
	
	@rm $(BUILD_DIR)/$(DOCKCROSS_CMD)
endef

bdwgc_sources:
	@if [ ! -d $(BDWGC_DIR) ]; then \
        git clone git://github.com/ivmai/bdwgc.git $(BDWGC_DIR); \
    fi

stark_sources:
	@if [ ! -d $(STARK_DIR) ]; then \
        git clone https://github.com/zippy1978/stark.git $(STARK_DIR); \
		cd $(STARK_DIR) && git checkout $(STARK_BRANCH); \
    fi

arm-unknown-linux-gnueabihf: bdwgc_sources stark_sources
	$(call compile_target,linux-armv6,$@)

arm-unknown-linux-musleabihf: bdwgc_sources stark_sources
	$(call compile_target,linux-armv6-musl,$@)

clean:
	@rm -rf $(BUILD_DIR)

archive:
	@rm -rf $(STARK_DIR)
	@rm -rf $(BDWGC_DIR)
	@cd $(BUILD_DIR) && zip -r $(ARCHIVE) *
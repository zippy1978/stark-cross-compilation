UNAME := $(shell uname)
RPI_TARGET := arm-unknown-linux-gnueabihf
BUILD_DIR := build

LINKER := arm-linux-gnueabihf-gcc:-lpthread -lrt
ifeq ($(UNAME), Darwin)
	LINKER := arm-linux-musleabihf-gcc:-static -lpthread -lrt
	RPI_TARGET := arm-unknown-linux-musleabihf
endif


all: rpi

rpi:
	@mkdir -p $(BUILD_DIR)
	@starkc -t $(RPI_TARGET)::+vfp2 -l "$(LINKER)" -r ../runtime/build/$(RPI_TARGET)/libstark.a -o $(BUILD_DIR)/main main.st

clean:
	@rm -rf $(BUILD_DIR)

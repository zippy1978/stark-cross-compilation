RPI_TARGET := arm-unknown-linux-gnueabihf
BUILD_DIR := build

all: rpi

rpi:
	@mkdir -p $(BUILD_DIR)
	@starkc -t $(RPI_TARGET)::+vfp2 -l "arm-linux-gnueabihf-gcc:-lpthread -lrt" -r ../runtime/build/$(RPI_TARGET)/libstark.a -o $(BUILD_DIR)/main main.st

clean:
	@rm -rf $(BUILD_DIR)
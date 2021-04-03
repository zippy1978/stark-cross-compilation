# Stark cross compilation

This repository is dedicated to cross-compilation of materials related to the Stark programming language.

For more information about the language, please refer to [The Stark programming language](https://github.com/zippy1978/stark) repository.

## Cross compiling runtime

At the moment only runtime can be cross-compiled.

In order to cross compile runtime make sure you have **[docker](https://www.docker.com/)** and **make** installed on your system.

Then clone this repository and call make from the runtime directory :

```bash
cd runtime
$ make
```

Once finsihed, target specific static library runtimes will be available in their respective **runtime/build/<target>** directory.

For example: **runtime/build/arm-unknown-linux-gnueabihf/libstark.a**

## Testing

A simple test program and its compilation Makefile is available in the test directory.

In order to run it, you will need to install cross compilation tools on Linux (does not work on macOS at the time):

```bash
$ sudo apt-get install gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf
```

Also, make sure you already cross-compiled the runtime as stated above.

Then use make from the test directory to cross compile the examle code:

```bash
cd test
$ make
```

This will create an executable binary targeting Raspberry Pi hardware (armv6: all versions) inside **test/build/main**.
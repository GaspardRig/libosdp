![OSDP - Open Supervised Device Protocol][4]

[![Build Status][1]][2]

This is an open source implementation of Open Supervised Device Protocol (OSDP)
developed by [Security Industry Association (SIA)][3]. The protocol is intended
to improve interoperability among access control and security products. OSDP
is currently in-process to become a standard recognized by the American National
Standards Institute (ANSI).

OSDP describes the communication protocol for interfacing one or more Peripheral
Devices (PD) to a Control Panel (CP). The OSDP specification describes the
protocol implementation over a two-wire RS-485 multi-drop serial communication
channel. Nevertheless, this protocol can be used to transfer secure data over
any physical channel. Have a look at our [protocol design documents][5] to get
a better idea.

## Salient Features of LibOSDP

- Supports secure channel communication (AES-128).
- Can be used to setup a PD or CP mode of operation.
- Exposes a well defined contract though `include/osdp.h`.
- No run-time memory allocation. All memory is allocated at init-time.
- Well designed source code architecure.

## Supported Commands and Replies

OSDP has certain command and reply IDs pre-registered. This implementation of
the protocol support only the most common among them. You can see [a list of
commands and replies and their support status in LibOSDP here][6].

## Dependencies

* cmake3 (host)

## Compile LibOSDP

To build libosdp you must have cmake-3.0 (or above) and a C compiler installed.
This repository produces a `libosdpstatic.a` and `libosdp.so`. You can link
these with your application as needed (-losdp or -losdpstatic). Have a look at
`sample/*` for details on how to consume this library.

```sh
mkdir build && cd build
cmake ..
make
make check
make DESTDIR=/your/install/path install
```

## Add libosdp to your cmake project

If you are familer with cmake, then adding libosdp to your project is super
simple. First off, add the following to your CMakeLists.txt

```cmake
include(ExternalProject)
ExternalProject_Add(ext_libosdp
	GIT_REPOSITORY    https://github.com/cbsiddharth/libosdp.git
	GIT_TAG           master
	SOURCE_DIR        ${CMAKE_BINARY_DIR}/libosdp/src
	BINARY_DIR        ${CMAKE_BINARY_DIR}/libosdp/build
	CONFIGURE_COMMAND cmake ${CMAKE_BINARY_DIR}/libosdp/src
	BUILD_COMMAND     make
	INSTALL_COMMAND   make install DESTDIR=${CMAKE_BINARY_DIR}/libosdp/install
)
include_directories("${CMAKE_BINARY_DIR}/libosdp/install/include")
link_directories("${CMAKE_BINARY_DIR}/libosdp/install/lib")
```

Next you must add `ext_libosdp` as a dependency to your target. That it! now
you can link your application to osdp library. Following example shows how you
can do this.

```cmake
set(OSDP_APP osdp-app)
list(APPEND OSDP_APP_SRC
    "src/main.c"
    "src/more_source_files.c"
    ...
)
add_executable(${OSDP_APP} ${OSDP_APP_SRC})
add_dependencies(${OSDP_APP} ext_libosdp)
target_link_libraries(${OSDP_APP} osdp)
```

This repository is a work in progress; read the `TODO` file for list of pending
tasks. Patches in those areas are welcome; open an issue if you find a bug.

[1]: https://travis-ci.org/cbsiddharth/libosdp.svg?branch=master
[2]: https://travis-ci.org/cbsiddharth/libosdp
[3]: https://www.securityindustry.org/industry-standards/open-supervised-device-protocol/
[4]: doc/osdp-logo.png
[5]: doc/osdp-design.md
[6]: doc/osdp-commands-and-replies.md

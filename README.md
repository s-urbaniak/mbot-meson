## Firmware for Makeblock Auriga board

### Introduction

This is a port from https://github.com/Makeblock-official/Makeblock-Libraries to meson and ninja
for building the Auriga board firmware without the Arduino IDE
by using meson, ninja, and the avr cross compilation suite.

### Prerequisites

The following packages must be installed:

- meson
- ninja
- avr-gcc
- avr-gcc-c++
- avr-libc
- avr-binutils
- avrdude
- arduino-core

### Usage

#### Build

```
$ meson --cross-file arduino_atmega2560_cross.txt build
$ cd build
$ ninja
```

#### Building the hex firmware

```
ninja firmware.hex
```

#### Flashing the Auriga board

The following assumes the Auriga board is available at /dev/ttyUSB0.

```
ninja ardup
```

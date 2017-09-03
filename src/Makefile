#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Simple makefile for the cortex-M0+ build system
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.i - Generates the <FILE>.i preprocessed output file
#      <FILE>.asm - Generates <FILE>.asm assembly output file
#      <FILE>.o - Builds <FILE>.o object file but does not link
#      build - Compiles all object files and link into a final executable
#      compile-all - Compiles all object files, but DOES NOT link
#      clean - remove all compiled objects, preprocessed outputs, assembly
#              outputs, executable files and build output files
#
# Platform Overrides:
#      PLATFORM - Supports two target platforms (HOST and MSP432)
#                 The host embedded system uses gcc
#                 The target embedded system uses arm-none-eabi-gcc
#------------------------------------------------------------------------------
include sources.mk

# Architectures Specific Flags
LINKER_FILE = msp432p401r.lds
CPU = cortex-m4
ARCH = thumb
SPECS = nosys.specs

# Compiler Flags and Defines
TARGET = c1m2
ifeq ($(PLATFORM), HOST)
    CC = gcc
	LD = ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99 -DHOST
	LDFLAGS = -Wl,-Map=$(TARGET).map
else
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99		\
		-mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) \
		-march=armv7e-m							\
		-mfloat-abi=hard						\
		-mfpu=fpv4-sp-d16						\
		-DMSP432
	LDFLAGS = -Wl,-Map=$(TARGET).map -T ../$(LINKER_FILE)
endif

OBJS = $(SOURCES:.c=.o)
ASM = $(SOURCES:.c=.asm)
PREP = $(SOURCES:.c=.i)

%.i: %.c
	$(CC) -E $< $(CFLAGS) $(INCLUDES) -o $@

%.asm: %.c
	$(CC) -S $< $(CFLAGS) $(INCLUDES) -o $@

%.o: %.c
	$(CC) -c $< $(CFLAGS) $(INCLUDES) -o $@

.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: build
build: compile-all
	$(CC) $(OBJS) $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $(TARGET).out

.PHONY: clean
clean:
	rm -f *.o $(TARGET).out $(TARGET).map *.asm *.i

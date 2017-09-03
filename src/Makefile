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
CFLAGS = -Wall -Werror -g -O0 -std=c99

# Compiler Flags and Defines
TARGET = c1m2
CPPFLAGS = -D$(PLATFORM) -MMD -MP
ifeq ($(PLATFORM), HOST)
    CC = gcc
    LD = ld
    OBJDMP = objdump
    SIZE = size
    LDFLAGS = -Wl,-Map=$(TARGET).map
else
    CC = arm-none-eabi-gcc
    LD = arm-none-eabi-ld
    OBJDMP = arm-none-eabi-objdump
    SIZE = arm-none-eabi-size
    CFLAGS += -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) \
        -march=armv7e-m							\
        -mfloat-abi=hard						\
        -mfpu=fpv4-sp-d16
    LDFLAGS = -Wl,-Map=$(TARGET).map -T ../$(LINKER_FILE)
endif

OBJS = $(SOURCES:.c=.o)
DEPS = $(SOURCES:.o=.d)

%.i: %.c
	$(CC) -E $< $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -o $@

%.asm: %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -o $@

%.o: %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -o $@

.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: build
build: compile-all
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) $(LDFLAGS) -o $(TARGET).out
	$(SIZE) -Atd $(OBJS) $(TARGET).out

$(TARGET).asm: build
	$(OBJDMP) --disassemble $(TARGET).out > $(TARGET).asm

.PHONY: clean
clean:
	rm -f *.o *.out *.map *.asm *.i *.d

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
# Make file to compile en two platforms, the host and MSP432
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Generate a object file call <FILE>.o
#      <FILE>.i - Generate a preprocessed file call <FILE>.i
#      <FILE>.asm - Generate a assembly file call <FILE>.asm
#      compile-all - Compiles all sources files but do not link 
#      build - Builds all the objects files
#      clean - Remove all the generated files
#
# Platform Overrides:
#      HOST - The host environment
#      MSP342 - The target enbedded system
#
#
# Author: Gonzales Loayza Pool Diego
# Date: 12/07/2020
#    
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = HOST

# General flags
GEN_FLAGS = -Wall \
	    -Werror \
	    -g \
	    -O0 \
	    -std=c99

# Target
TARGET = c1m2

ifeq ($(PLATFORM),MSP432)
	# Architectures Specific Flags
	LINKER_FILE = -T msp432p401r.lds 
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	EXTRA = -mthumb \
		-mfloat-abi=hard \
		-mfpu=fpv4-sp-d16

	# Compiler Flags and Defines
	CC = arm-none-eabi-gcc 
	LD = arm-none-eabi-ld
	LDFLAGS = -Wl,-Map=$(TARGET).map -T -L ../ $(LINKER_FILE)
	CFLAGS = $(GEN_FLAGS) $(EXTRA) -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS)
	CPPFLAGs = -DMSP432 $(INCLUDES)

	# Architecture specific source codes
	SOURCE =  main.c \
		  memory.c \
		  interrupts_msp432p401r_gcc.c \
		  startup_msp432p401r_gcc.c \
		  system_msp432p401r.c

	SIZE = arm-none-eabi-size

else
   	# Compiler Flags and Defines
	CC = gcc
	CFLAGS = $(GEN_FLAGS)
	CPPFLAGS = -DHOST $(INCLUDES)
	LDFLAGS = -Wl,-Map=$(TARGET).map
	SIZE = size

endif

OBJS = $(SOURCES:.c=.o)
DEPS = $(SOURCES:.c=.d)
ASMS = $(SOURCES:.c=.asm)
PREP = $(SOURCES:.c=.i)

%.o:%.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@
	
%.asm:%.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

%.i:%.c
	$(CC) -E $< $(CPPFLAGS) -o $@

%.d:%.c
	$(CC) -E -M $< $(CPPFLAGS) -o $@

.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@

.PHONY: clean
clean:
	rm -f $(OBJS) $(DEPS) $(ASMS) $(PREP) $(TARGET).out $(TARGET).map


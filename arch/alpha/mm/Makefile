#
# Makefile for the linux alpha-specific parts of the memory manager.
#

ccflags-y := -Wall

obj-y	:= init.o fault.o

obj-$(CONFIG_DISCONTIGMEM) += numa.o

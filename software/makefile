# Project:  tinyCharger
# Author:   Stefan Wagner
# Year:     2021
# URL:      https://easyeda.com/wagiminator
#           https://github.com/wagiminator
#
# Type "make help" in the command line.

# Input and Output File Names
SKETCH  = TinyCharger_v1.0.ino
TARGET  = tinycharger

# Microcontroller Options
DEVICE  = attiny85
CLOCK   = 8000000
PROGRMR = usbasp
LFUSE   = 0xE2
HFUSE   = 0xD5
EFUSE   = 0xFF

# Commands
AVRDUDE = avrdude -c $(PROGRMR) -p $(DEVICE)
COMPILE = avr-gcc -Wall -Os -flto -mmcu=$(DEVICE) -DF_CPU=$(CLOCK) -x c++ $(SKETCH)
CLEAN   = rm -f *.lst *.obj *.cof *.list *.map *.eep.hex *.o *.s

# Symbolic Targets
help:
	@echo "Use the following commands:"
	@echo "make all       compile and build $(TARGET).bin/.hex/.asm for $(DEVICE)"
	@echo "make hex       compile and build $(TARGET).hex for $(DEVICE)"
	@echo "make asm       compile and disassemble to $(TARGET).asm for $(DEVICE)"
	@echo "make bin       compile and build $(TARGET).bin for $(DEVICE)"
	@echo "make upload    compile and upload to $(DEVICE) using $(PROGRMR)"
	@echo "make fuses     burn fuses of $(DEVICE) using $(PROGRMR) programmer"
	@echo "make install   compile, upload and burn fuses for $(DEVICE)"
	@echo "make clean     remove all build files"

all:	buildbin buildhex buildasm removetemp size

bin:  buildbin removetemp size

hex:	buildbin buildhex removetemp size removebin

asm:	buildbin buildasm removetemp size removebin

install: upload fuses

upload:	hex
	@echo "Uploading to $(DEVICE) ..."
	@$(AVRDUDE) -U flash:w:$(TARGET).hex:i

fuses:
	@echo "Burning fuses of $(DEVICE) ..."
	@$(AVRDUDE) -U lfuse:w:$(LFUSE):m  -U hfuse:w:$(HFUSE):m  -U efuse:w:$(EFUSE):m

clean:
	@echo "Cleaning all up ..."
	@$(CLEAN)
	@rm -f $(TARGET).bin $(TARGET).hex $(TARGET).asm

buildbin:
	@echo "Building $(TARGET).bin for $(DEVICE) @ $(CLOCK)Hz ..."
	@$(COMPILE) -o $(TARGET).bin

buildhex:
	@echo "Building $(TARGET).hex ..."
	@avr-objcopy -j .text -j .data -O ihex $(TARGET).bin $(TARGET).hex

buildasm:
	@echo "Disassembling to $(TARGET).asm ..."
	@avr-objdump -d $(TARGET).bin > $(TARGET).asm

size:
	@echo "FLASH: $(shell avr-size -d $(TARGET).bin | awk '/[0-9]/ {print $$1 + $$2}') bytes"
	@echo "SRAM:  $(shell avr-size -d $(TARGET).bin | awk '/[0-9]/ {print $$2 + $$3}') bytes"

removetemp:
	@echo "Removing temporary files ..."
	@$(CLEAN)

removebin:
	@echo "Removing $(TARGET).bin ..."
	@rm -f $(TARGET).bin

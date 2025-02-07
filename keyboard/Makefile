PROJECT=fortuna-terminal-keyboard
OBJECTS=main.o uart.o ps2.o videoout.o
AVRDUDE_FLAGS=-p attiny2313 -C +./avrdude_gpio.conf -c pi_rst -B 0.5 -V
FUSES=-U efuse:w:0xff:m -U hfuse:w:0xdf:m -U lfuse:w:0x60:m   # external clock from VGA uc, divide by 8
ROLLBACK_FUSES=-U lfuse:w:0x64:m -U hfuse:w:0xdf:m -U efuse:w:0xff:m
MCU=attiny2313a
F_CPU=3000000UL
BAUD=38400

CC=avr-gcc
CPPFLAGS=-std=c11 -Wall -Wextra -Os -DF_CPU=${F_CPU} -DBAUD=${BAUD} -mmcu=${MCU} -ffunction-sections -fdata-sections -I../common

all: ${PROJECT}.hex

ps2.o: scancodes.c

${PROJECT}.hex: ${PROJECT}.elf
	avr-objcopy -j .text -j .data -O ihex $< $@
	arm-linux-gnueabihf-size ${PROJECT}.hex

${PROJECT}.elf: ${OBJECTS}
	$(CC) -mmcu=${MCU} -o $@ $^ -Wl,--gc-sections
	avr-size -C --mcu=${MCU} ${PROJECT}.elf	

size: ${PROJECT}.elf
	avr-size -C --mcu=${MCU} ${PROJECT}.elf	

test-connection:
	sudo avrdude ${AVRDUDE_FLAGS}

clean:
	rm -f boot.c *.elf *.o *.hex z80/*.rom

upload: ${PROJECT}.hex
	sudo avrdude ${AVRDUDE_FLAGS} -U flash:w:$<:i

fuse:
	sudo avrdude ${AVRDUDE_FLAGS} ${FUSES}

rollback-fuse:
	sudo avrdude ${AVRDUDE_FLAGS} ${ROLLBACK_FUSES}

talk:
	@echo "To exit, type Ctrl+A, and then Shift+K."
	@sleep 1
	screen /dev/ttyAMA0 38400

# vim: set ts=8 sts=8 sw=8 noexpandtab:

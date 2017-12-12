# RF-Bridge-EFM8BB1
RF-Bridge-EFM8BB1

The Sonoff RF Bridge is only supporting one protocol with 24 bits.<br/>
The Idea is to write a alternative firmware for the onboard EFM8BB1 chip.

# Hardware
There are the pins C2 & C2CK on the board. With a Arduino you can build a programmer to read/erase and program the flash.
Software for the Arduino: https://github.com/conorpp/efm8-arduino-programmer

# Software
The project is written with Simplicity Studio 4. The resulting *.hex file can be programmed on the EFM8BB1.

# First results
The reading of RF signals is already working:<br/>
Sending start sniffing: 0xAA 0xA6 0x55<br/>
Receiving AKN: 0xAA 0xA0 0x55<br/>

Sending stop sniffing: 0xAA 0xA7 0x55<br/>
Receiving AKN: 0xAA 0xA0 0x55<br/>

## RF decode from Rohrmotor24.de remote (40 bit of data):
0xAA: uart sync init<br/>
0xA6: sniffing active<br/>
0x06: data len<br/>
0x01: protocol identifier<br/>
0xD0-0x55: data<br/>
0x55: uart sync end

STOP:<br/>
Binary: 10101010 10100110 00000110 00000001 11010000 11111001 00110010 00010001 01010101 01010101<br/>
Hex: AA A6 06 01 D0 F9 32 11 55 55<br/>
DOWN:<br/>
Binary: 10101010 10100110 00000110 00000001 11010000 11111001 00110010 00010001 00110011 01010101<br/>
Hex: AA A6 06 01 D0 F9 32 11 33 55<br/>

## RF decode from Seamaid_PAR_56_RGB remote (24 bit of data):
Light ON:<br/>
Binary: 10101010 10100110 00000100 00000010 00110010 11111010 10001111 01010101<br/>
Hex: AA A6 04 02 32 FA 8F 55<br/>

## Transmiting by command 0xA5
This is the original implemented RF transmit command<br/>
Hex: AA A5 24 E0 01 40 03 84 D0 03 58 55<br/>

0xAA: uart sync init<br/>
0x24-0xE0: Tsyn<br/>
0x01-0x40 Tlow<br/>
0x03-0x84 Thigh<br/>
0xD0-0x58 24bit Data<br/>

The low time of the SYNC is default 2000µs, duty cycle of the high bit 70%, 30% of the low bit.<br/>

## Transmiting by command 0xA8
There is a new command in the firmware to be able to send RF data.<br/>

Hex: AA A8 06 01 D0 F9 32 11 33 55<br/>

0xAA: uart sync init<br/>
0xA8: transmit RF data<br/>
0x06: data len<br/>
0x01: protocol identifier (ROHRMOTOR24)<br/>
0xD0-0x55: data<br/>
0x55: uart sync end

Universal transmit by command 0xA8<br/>
Hex: AA A8 0D 7F 12 C0 05 DC 02 BC 46 01 2C 1E 08 AA 55<br/>

0xAA: uart sync init<br/>
0xA8: transmit RF data<br/>
0x0D: data len<br/>
0x7F: protocol identifier 0x7F<br/>
0x12-0xC0: SYNC_HIGH<br/>
0x05-0xDC: SYNC_LOW<br/>
0x02-0xBC: BIT_HIGH_TIME<br/>
0x46: BIT_HIGH_DUTY<br/>
0x01-0x2C: BIT_LOW_TIME<br/>
0x1E: BIT_LOW_DUTY<br/>
0x08: BIT_COUNT + SYNC_BIT_COUNT in front of RF data<br/>
0x1E: RF data to send<br/>

# Next Steps
Add full support for the original firmware<br/>
Add ESPurna support:<br/>
A new protocol have to be implemented to support more RF signals -> have to be defined!
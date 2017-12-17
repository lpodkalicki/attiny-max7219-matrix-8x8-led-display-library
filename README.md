# MAX7219/MAX7221 8x8 Matrix LED Display Library
This is tinyAVR (ATtiny13, ATtiny25, ATtiny45, ATtiny85) library for LED display modules based on MAX7219/MAX7221 chip.

Modules based on MAX7219/MAX7221 provide three signal connections (CLK, DIN and CS) and two power connections (VCC and GND). Signal pins can be connected to any of digital pins of the AVR chip. Signal pins configuration is defined at the top of library header file, where it can be modifed.

## Key Features
This lightweight library has the following features:
* set single pixel
* set all 8 LEDs in column or row
* clear display
* brightness control
* software SPI

## Example Code
This example project of "Random Flickering Pixels" demonstrates basic usage of the library:

```c
#include <stdint.h>
#include <util/delay.h>
#include "max7219.h"

#define LFSR_SEED	(213)

static uint16_t
prng_lfsr16(void)
{
        static uint16_t cnt16 = LFSR_SEED;
        return (cnt16 = (cnt16 >> 1) ^ (-(cnt16 & 1) & 0xB400));
}

int
main(void)
{
	uint8_t row, col, i = 0;

	/* setup */
	MAX7219_init();
	MAX7219_set_intensity(8);

	/* loop */
	while (1) {
		row = (prng_lfsr16() + col) % 8;
		col = (prng_lfsr16() + row) % 8;
		MAX7219_set_pixel(row, col, ++i & 0x01);
		_delay_ms(5);
	}
}

```

## API Documentation (max7219.h)

```c
/**
 * Initialize display driver.
 * Clock pin, data pin and chip select pin
 * are defined at the top of this file.
 */
void MAX7219_init(void);

/**
 * Set status of a single pixel.
 * @param row: row number from range <0, 7>
 * @param col: column number from range <0, 7>
 */
void MAX7219_set_pixel(uint8_t row, uint8_t col, bool value);

/**
 * Set all 8 LEDs in a row.
 * @param row: row number from range <0, 7>
 * @param value: row value (each bit represents a LED in a row, i.e. 0b00001111)
 */
void MAX7219_set_row(uint8_t row, uint8_t value);

/**
 * Set all 8 LEDs in a column.
 * @param column: column number from range <0, 7>
 * @param value: column value (each bit represents a LED in a column, i.e. 0b00001111)
 */
void MAX7219_set_column(uint8_t column, uint8_t value);

/**
 * Set brightness of the display.
 * @param value: intensity from range <0, 15>
 */
void MAX7219_set_intensity(uint8_t value);

/**
 * Clear display. 
 */
void MAX7219_clear(void);
```

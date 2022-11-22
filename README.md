# PlatformIO training session

## Initialising the project

    pio project init

## Adding Teensy 4.1 to `platformio.ini` config file

https://docs.platformio.org/en/latest/boards/teensy/teensy41.html
```ini
[env:teensy41]
platform = teensy
board = teensy41
```

## Installing necessary libraries

    pio pkg install -l https://github.com/TeamSunride/Arduino-LSM6DSO32

## Create `src/main.cpp` file

```cpp
/*
 * An example of how the LSM6DSO32 can be used by reading directly from the accel and gyro registers
 * The built-in Fifo is not being used.
 *
 */


#include <Arduino.h>
#include "LSM6DSO32.h"

// The LSM6DSO32 can be used with either SPI or I2C, and this library supports both, using Protocol: https://github.com/TeamSunride/Protocol
#define CS_pin 40
LSM6DSO32 LSM(CS_pin, SPI, 4000000); // spi protocol constructor


void setup() {
    // Setup Serial on 115200 baud
    Serial.begin(115200);

    // begin() initialises the communication protocol.
    LSM.begin();

    // default_configuration() configures the device. - Specific settings can be set after calling this:
    LSM.default_configuration();
    // for example:
    // LSM.set_interrupts_active_low(false);
}

void loop() {
    // directly read from the accel and gyro registers - not using the fifo
    Vector<double, 3> acc = LSM.get_accel();
    Vector<double, 3> gyr = LSM.get_gyro();
    Serial.printf("Acc: %lf, %lf, %lf\n     Gyr: %lf, %lf, %lf\n", acc[0], acc[1], acc[2], gyr[0], gyr[1], gyr[2]);

    // Be aware of the ODR (Output data rate) that is set - reading at a rate higher than this means you will be reading stale data when using direct reads.
    // Note: The ODR can be set in setup() using e.g.    LSM.set_accel_ODR(OUTPUT_DATA_RATES::ODR_833_HZ);    and     LSM.set_gyro_ODR(OUTPUT_DATA_RATES::ODR_833_HZ);
    delay(10);
}
```

## Build example

    pio run

## Upload and monitor example using 'targets'

    pio run -t upload -t monitor

### Output:

```
Acc: 5.704111, 0.086136, 8.249905
     Gyr: 0.140000, -1.330000, -1.540000
Acc: 5.503127, 0.143560, 7.733090
     Gyr: -395.920002, 28.210000, 111.650000
Acc: 5.637116, 0.133989, 8.068063
     Gyr: -231.210001, 15.400000, 46.130000
Acc: 5.665828, 0.114848, 8.202052
     Gyr: -31.570000, 3.010000, 7.350000
Acc: 5.684969, 0.114848, 8.240334
     Gyr: -23.100000, 2.730000, -0.560000
Acc: 5.704111, 0.105277, 8.269046
```
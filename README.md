<div align="center">
 <h1>SerialLedger board library(v0.1 beta 1)</h1>

Portable, allocation-free C99 telemetry transport and C++11 typed reading helpers.
Arduino, ESP32, and other boards send newline-delimited JSON; the separately installed
Python companion records CSV and exports Excel workbooks on your computer.

[![GitHub version](https://img.shields.io/github/v/tag/pravennn08/SerialLedger?include_prereleases&sort=semver)](https://github.com/pravennn08/SerialLedger/tags)
![C](https://img.shields.io/badge/Baremetal-A8B9CC?logo=c&logoColor=black)
![C++](https://img.shields.io/badge/C%2B%2B-00599C?logo=cplusplus&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00878F?logo=arduino&logoColor=white)
![ESP32](https://img.shields.io/badge/ESP32-E7352C?logo=espressif&logoColor=white)
[![CI](https://github.com/pravennn08/SerialLedger/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/pravennn08/SerialLedger/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-red)](./LICENSE.md)

</div>

## Installation

The beta is published on [PlatformIO](https://registry.platformio.org/libraries/engrpraven08/SerialLedger).
Arduino Library Manager registration was accepted in
[PR #9367](https://github.com/arduino/library-registry/pull/9367).
If the library has not appeared after refreshing the index, use the ZIP
installation below while indexing completes. The board-library source is hosted at
<https://github.com/pravennn08/SerialLedger>.

In Arduino IDE, choose **Sketch > Include Library > Add .ZIP Library** and select
the prepared `SerialLedger-0.1.0-beta.1.zip`. Open **File > Examples > SerialLedger >
BasicTelemetry**. Install the board's core separately before compiling/uploading.

For PlatformIO, extract the ZIP and copy its `SerialLedger` folder into your
project's `lib/` directory. The layout should be `lib/SerialLedger/src/SerialLedger.h`.
No SerialLedger registry entry is required. This package includes its own
`library.json` manifest. The DHT example needs the separate Adafruit sensor libraries.

After the library is listed in Arduino Library Manager, search for **SerialLedger**
by **Raven Magbanua**, install it, then open **File > Examples > SerialLedger >
BasicTelemetry**.

To install from PlatformIO, add this to your environment's `platformio.ini`:

```ini
lib_deps =
    engrpraven08/SerialLedger@0.1.0-beta.1
```

The library contains the C99 API and the C++11 wrapper. Install the Python CLI
separately on your computer to create CSV/Excel files:

```sh
uv tool install --python 3.11 "serial-ledger==0.1.0b1"
serial-ledger ports
serial-ledger record --port COM7 --baud 115200 --live --output data/sensor.csv
serial-ledger export data/sensor.csv data/sensor.xlsx
```

Use your board's serial port and close other serial monitors before recording.
Stop the recorder with Ctrl+C before exporting. See the
[Python package](https://pypi.org/project/serial-ledger/0.1.0b1/) for the companion.

For C/C++ with CMake 3.28+, extract the ZIP, then:

```cmake
add_subdirectory(path/to/SerialLedger serialledger)
target_link_libraries(your_target PRIVATE SerialLedger::Core) # C99
# Or SerialLedger::Cpp for C++11.
```

The Arduino `Print` adapter is available when `ARDUINO` is defined. Otherwise,
provide a write callback for your UART/transport. See [the API guide](docs/BOARD_API.md).

## Arduino example

```cpp
#include <SerialLedger.h>

SerialLedger logger(Serial);

void setup() { Serial.begin(115200); }

void loop() {
    SerialLedgerReading reading;
    reading.addFloat("temperature_C", 25.4); // Replace with your sensor value.
    reading.addUInt("time_ms", millis());
    if (logger.writeReading(reading) != SL_OK) {
        logger.writeLine("[ERROR] Could not send reading");
    }
    delay(2000);
}
```

Hardware capture/export has been tested on ESP32-WROOM-32, Arduino Uno, and classic
ATmega328P Nano. The portable API can target other boards but these are the verified
ones. The beta API may change before a stable release. The fixed reading buffer
defaults to 192 bytes; float formatting supports 0–6 decimal places and finite
values with magnitude up to 1e9. There is no automatic reconnect or retransmission.

MIT licensed; see [LICENSE.md](LICENSE.md).
See [CHANGELOG.md](CHANGELOG.md) for board-library release history.
Source, PC companion, and support: <https://github.com/pravennn08/serial-ledger>.

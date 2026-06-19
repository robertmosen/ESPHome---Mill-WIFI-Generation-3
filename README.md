ESPHome custom firmware for the smart plug Mill WIFI gen. 3

First of all, i am no programmer so bare with me.

I bougt this smart plug second hand from a thrift store and it did not perform as i expected. It connected just fine to the app but lost the WIFI after a while. The firmware upgrade in the Mill app couldn't complete. Overall it was a bit unstable.

So i opened it and found a ESP32 wroom inside. Ofcourse i had to adapt it to ESPHome.

After some reverse engineering i had these figured out:

Button: GPIO4

Green LED: GPIO18

Red LED: GPIO19



I2C SDA: GPIO21

I2C SCL: GPIO22

UART TX: GPIO17

UART RX: GPIO18

The green LED pin is shared with UART rx pin so when reading serial data the led disrupts. I guess the green LED is used in the original Mill firmware when serial is inactive and vice versa.

I found an I2C device at adress 0x40. Original Mill firmware showed both temperature and humidity. Tried every supported temp+rh and single temp -device supported by ESPhome, but no luck. I'm guessing that the device is proprietary.

There is a second circuit which reads power usage and reports it by UART to the ESP32. I got it working in a state where i could read HEX data. For me this is a feature i don't actually need and i have no way to determine known power usage on reference devices.

To get it working properly you need to do this:

1 Set log level to very verbose

2 Connect nothing, red hex value

3 Connect device with low know power usage, read hex value

4 Connect device with high know power usage, read hex value

5 Add sensor with calibrated readout with linear values to your yaml (actually don't know if it's linear but this is where i'd start). https://esphome.io/components/sensor/

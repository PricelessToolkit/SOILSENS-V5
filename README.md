<img src="img/SOILSENS-V5.png"/>


🤗 Please consider subscribing to my [YouTube channel](https://www.youtube.com/@PricelessToolkit/videos) Your subscription goes a long way in backing my work. if you feel more generous, you can buy me a coffee


[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/U6U2QLAF8)

# Wired "SOILSENS-V5" and Wireless ["SOILSENS-V5W"](https://github.com/PricelessToolkit/SOILSENS-V5W)

The SOILSENS-V5 is a reliable wired capacitive soil moisture sensor. Unlike cheap sensors from AliExpress, it has a longer design, keeping electronics away from the soil. It has no 3.3V regulator and can be powered from 3-5V. It is based on the MLC555 timer, making it much more energy-efficient. 


## 🛒 Can be purchased from http://www.PricelessToolkit.com

## Connection Diagram

Connect the sensor to the ESP32 board following the wiring instructions below:

| Sensor | Sensor Wire | Microcontroller |
|--------|-------------|-----------------|
| GND    | Black       | GND            |
| VCC    | Red         | 3.3-5V         |
| A0     | White       | GPIO0 "A0"     |



> [!NOTE]
> When powered with 3.3V, the output in air is ~2.8V "ADC 4050" and in water is ~1.4V "ADC 1950".

------------------

## ESPhome YAML

```yaml


sensor:
  - platform: adc  # ADC documentation https://esphome.io/components/sensor/adc.html
    pin: A0
    name: "Soil Moisture"
    update_interval: 1s
    unit_of_measurement: "%"
    icon: "mdi:water-percent"
    attenuation: 11dB
    filters:
    - calibrate_linear:
        - 2.8 -> 0.00    # # Calibrate Min in dry soil
        - 1.4 -> 100.00  # Calibrate MAX in 100% wet soil 
    - lambda: |
        if (x < 0) return 0; 
        else if (x > 100) return 100;
        else return (x);


```
ESPhome ADC Documentation https://esphome.io/components/sensor/adc.html

### Calibration Process for ESPhome
1. Upload the YAML example
2. Check the wireless Logs for the minimum and maximum values
3. Change the `- calibrate_linear:` "2.8 and 1.4" values.

------------------

## ESP32 Arduino Code

```cpp
#include <driver/adc.h>

// Adjust the values.
int DrySoil = 4050;
int HumidSoil = 1950;

void setup() {
  Serial.begin(9600); // Initialize serial communication at 9600 baud

  // Configure ADC1 channel 3
  adc1_config_width(ADC_WIDTH_BIT_12); // Set ADC width to 12 bits (0-4095)
  adc1_config_channel_atten(ADC1_CHANNEL_0, ADC_ATTEN_DB_11); // Set attenuation to 11dB for higher input voltage range
}

void loop() {
  // Read the raw value from the soil sensor
  int soilRaw = adc1_get_raw(ADC1_CHANNEL_0); // Get the raw ADC value from channel 0
  
  // Map the reading to a percentage (0 to 100)
  // The map() function scales the raw value from the range 4050-1950 to 0-100
  int soilPercent = map(soilRaw, DrySoil, HumidSoil, 0, 100);
  
  // Constrain the value to ensure it doesn't exceed 100%
  // The constrain() function limits the value to be between 0 and 100
  soilPercent = constrain(soilPercent, 0, 100);
  
  // Print the raw value and the mapped percentage
  Serial.print("Soil Sensor Raw Value: ");
  Serial.print(soilRaw);
  Serial.print("Soil Moisture: ");
  Serial.print(soilPercent);
  Serial.println("%");

  // Wait for a short period before taking another reading
  delay(1000); // Delay for 1 second (1000 milliseconds)
}


```

### Calibration Process for Arduino Code

1. Open the serial monitor and set the baud rate to 9600.
2. Record the sensor value when the probe is in the dry soil. This value, referred to as "DrySoil" represents 0% soil moisture and typically ranges between 3900-4100.
3. Insert the probe into a maximum wet soil not exceeding the tree line on the board. This value, referred to as "HumidSoil", representing 100% soil moisture.
4. Insert the recorded "DrySoil" and "HumidSoil" values into the code.

------------------

## Frequently Asked Questions
### Why is my sensor giving inconsistent readings?
Due to the capacitive sensing principle, readings can vary based on soil moisture, tightness, and insertion depth. Repeated insertions might loosen the soil, affecting subsequent readings.

### Why are my recorded DrySoil and HumidSoil values are different from the examples?
Individual sensor variations and environmental factors (like air humidity) can cause differences. Adjust the test code accordingly to the recorded values.

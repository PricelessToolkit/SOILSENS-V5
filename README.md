<img src="https://raw.githubusercontent.com/PricelessToolkit/SOILSENS-V5/main/img/banner.jpg"/>

🤗 Please consider subscribing to my [YouTube channel](https://www.youtube.com/@PricelessToolkit/videos) Your subscription goes a long way in backing my work. if you feel more generous, you can buy me a coffee


[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/U6U2QLAF8)

# Wired "SOILSENS-V5" and Wireless "SOILSENS-V5W"

The SOILSENS-V5 is a reliable wired capacitive soil moisture sensor. Unlike cheap sensors from AliExpress, it has a longer design, keeping electronics away from the soil. It has no 3.3V regulator and can be powered from 2-5V. It is based on the MLC555 timer, making it much more energy-efficient. 


## 🛒 Can be purchased from http://www.PricelessToolkit.com

## Connection Diagram

Connect the sensor to the main control board following the wiring instructions below:

| Sensor | Microcontreller |
|--------|-----------------|
|  GND   |   GND           |
|  VCC   |   3.3-5V        |
|  A0    |   Analog Input  |

> [!NOTE]
> The VCC and output difference is 0.6V. Therefore, when powered with 3.3V, the output in air is 2.8V and in water is 1.30V.

## Initial Test Code

First, we need to calibrate the sensor to establish its measurement range.

```cpp
void setup() {
  Serial.begin(9600); // Initialize serial communication at 9600 bps
}

void loop() {
  Serial.println(analogRead(A0)); // Read and print the sensor value
  delay(100);
}
```

## Calibration Process
### Calibration Range
1. Open the serial monitor and set the baud rate to 9600.
2. Record the sensor value when the probe is exposed to air. This value, referred to as "Air Value," represents 0% soil moisture and typically ranges between 520-640.
3. Insert the probe into a cup of water to the recommended depth (not exceeding the tree line on the board). The value recorded here, when it reads xx, is the "Water Value," representing 100% soil moisture.

### Section Settings
The sensor's humidity range is divided into three sections based on the recorded values:

- Dry: (Air Value, Air Value - intervals]
- Wet: (Air Value - intervals, Water Value + intervals]
- Very Wet: (Water Value, Water Value + intervals]
Note: The actual values may vary due to soil tightness, insertion depth, and other environmental factors. Humidity is inversely proportional to the sensor reading.

### Final Test Code
Insert the recorded Air and Water values into the following code:


```cpp
/***************************************************

 Created 2024-07-11
 By PricelessToolkit

 This example reads Analog Capacitive Soil Moisture Sensor "SOILSENS-V5".

 ****************************************************/

const int AirValue = 600;   // Replace with the value recorded in the air
const int WaterValue = 0;   // Replace with the value recorded in the water

int intervals = (AirValue - WaterValue) / 3;
int soilMoistureValue = 0;

void setup() {
  Serial.begin(9600); // Initialize serial communication at 9600 bps
}

void loop() {
  soilMoistureValue = analogRead(A0);  // read the soil moisture sensor
  if (soilMoistureValue > WaterValue && soilMoistureValue < (WaterValue + intervals)) {
    Serial.println("Very Wet");
  } else if (soilMoistureValue > (WaterValue + intervals) && soilMoistureValue < (AirValue - intervals)) {
    Serial.println("Wet");
  } else if (soilMoistureValue < AirValue && soilMoistureValue > (AirValue - intervals)) {
    Serial.println("Dry");
  }
  delay(100);
}

```

## Frequently Asked Questions
### Why is my sensor giving inconsistent readings?
Due to the capacitive sensing principle, readings can vary based on soil moisture, tightness, and insertion depth. Repeated insertions might loosen the soil, affecting subsequent readings.

### What if my recorded Air and Water values are different from the examples?
Individual sensor variations and environmental factors (like air humidity) can cause differences. Adjust your recorded values accordingly in the test code.

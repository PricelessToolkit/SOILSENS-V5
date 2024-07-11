# Soil Moisture Sensor Connection and Calibration Guide

## Connection Diagram

Connect the sensor to the main control board following the wiring instructions below:

| Sensor | Microcontreller |
|--------|-----------------|
|  GND   |   GND           |
|  VCC   |   3.3-5V        |
|  A0    |   Analog Input  |



## Initial Test Code

To begin, we need to calibrate the sensor to establish its measurement range.

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
  soilMoistureValue = analogRead(A0);  // Insert the sensor into soil
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

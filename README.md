# Sound-Detector_LED-Brightness-based-on-Ambient-Noise
Control the brightness of an LED based on ambient sound levels.


HARDWARE
-
- Arduino board (Uno, Nano, Mega, etc.)
- SparkFun Sound Detector ([reference](https://www.sparkfun.com/sparkfun-sound-detector-with-headers.html))
- 3V 1W LED ([reference](https://www.adafruit.com/product/518))
- 2.2KΩ resistor
- NPN transistor (TIP120)
- Breadboard & jumper wires
- Power Supply for LED (3V, ≥500 mA)

WARNING!
-
The 3V 1W LED is much brighter than a typical LED, so we need to handle it carefully: you can’t drive it directly from an Arduino pin, because the Arduino’s pins can only source ~20 mA max, and 1W at 3V draws ~333 mA. The use of a transistor is required (explained below).

TIP120 Pins: see reference image [here](https://github.com/kingston-hackSpace/Sound-Detector_LED-Brightness-based-on-Ambient-Noise/blob/main/TIP120-NPN-Darlington-Transistor-Pin-Configuration.jpg)

WIRING
-

| LED             | Connect to                              |
| --------------- | --------------------------------------- |
| **Positive lead (+)** | Power supply positive (+) |
| **Negative lead (-)** | TIP120 Collector|



| TIP 120 Pin      | Connect to                              |
| --------------- | --------------------------------------- |
| **Base**        | Arduino PWM pin → 2.2KΩ resistor → TIP Base |
| **Collector**   | LED negative (–)                        |
| **Emitter**     | Power supply GND (also Arduino GND)     |


| Sound Detector  | Connect to                              |
| --------------- | --------------------------------------- |
| **Envelope**    | Arduino A0                              |
| **VCC**         | Arduino 5V                              |
| **GND**         | Arduino GND                             |



***Notes:***

Common ground is essential: Arduino GND, TIP120 emitter, and LED power supply GND must all be connected.

Base resistor (2.2KΩ) protects the Arduino and prevents oscillations.

The TIP120 allows the Arduino to safely modulate PWM to dim the 1W LED.


ARDUINO CODE
-
```
// Pin definitions
const int soundPin = A0;  // Envelope output
const int ledPin = 9;     // PWM pin for LED

// Variables for smoothing
float smoothedSound = 0;
float smoothingFactor = 0.1;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600); // Optional: for debugging
}

void loop() {
  int rawSound = analogRead(soundPin); // 0-1023
  // Smooth the reading
  smoothedSound = smoothedSound * (1 - smoothingFactor) + rawSound * smoothingFactor;
  
  // Map analog reading to LED brightness (0-255)
  int ledBrightness = map(smoothedSound, 0, 1023, 0, 255);
  ledBrightness = constrain(ledBrightness, 0, 255); // Ensure valid range
  
  analogWrite(ledPin, ledBrightness);

  // Optional: print for debugging
  Serial.print("Raw: "); Serial.print(rawSound);
  Serial.print(" | Smoothed: "); Serial.println(smoothedSound);

  delay(10); // small delay for smoother visual response
}
```

HOW IT WORKS
-
The Envelope output gives a voltage proportional to the sound amplitude.

The Arduino reads this analog voltage continuously.

Using map() and analogWrite(), the LED brightness scales with sound level.

The smoothing factor makes the LED respond gradually instead of flickering with every tiny noise spike.

MAP() FUNCTION
-
More about the map() function: https://docs.arduino.cc/language-reference/en/functions/math/map/

# Sound-Detector_LED-Brightness-based-on-Ambient-Noise
Control the brightness of an LED based on ambient sound levels.


EQUIPMENT
-
- Arduino board (Uno, Nano, Mega, etc.)
- SparkFun Sound Detector (https://www.sparkfun.com/sparkfun-sound-detector-with-headers.html)
- 3V 1W LED (https://www.adafruit.com/product/518) 
- 220 Ω resistor
- N‑channel MOSFET (e.g., IRL540, logic-level) or NPN transistor (e.g., TIP120)
- Breadboard & jumper wires

WARNING!
-
The 3V 1W LED is much brighter than a typical LED, so we need to handle it carefully: you can’t drive it directly from an Arduino pin, because the Arduino’s pins can only source ~40 mA max, and 1W at 3V draws ~333 mA. The use of a MOSFET is required (explained below).

WIRING
-

SparkFun Sound Detector → Arduino

GND → GND

VCC → 5V

Envelope (OUT) → A0

LED → Arduino

Anode → PWM pin (e.g., D9) via 220 Ω resistor

Cathode → GND

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

More about the map() function: https://docs.arduino.cc/language-reference/en/functions/math/map/

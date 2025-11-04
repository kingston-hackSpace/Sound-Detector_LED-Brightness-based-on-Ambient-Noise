# Sound-Detector_LED-Brightness-based-on-Ambient-Noise
Control the brightness of an LED based on ambient sound levels.


EQUIPMENT
-
- Arduino board (Uno, Nano, Mega, etc.)
- SparkFun Sound Detector
- LED 
- 220 Ω resistor (for single LED)
- Breadboard & jumper wires


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

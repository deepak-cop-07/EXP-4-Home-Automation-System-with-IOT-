# EXP-4-Home-Automation-System-with-IOT

# Aim:
	To make a Lamp at home (230 V AC) On / Off using ESP8266, IFTT Google Assistance and Blynk IoT mobile application. 
# Hardware / Software Tools required :
PC with Internet connection
Micro USB cable
Wifi connection for ESP8266 (Use any mobile hotspot or Router)
ESP8266 Board
Mobile Phone with Blynk App installed
IFTT for Google Voice Assistance
9 W Bulb and Relay control
Arduino software 
Jumper Wires

# Circuit Diagram:
<img width="1286" height="661" alt="image" src="https://github.com/user-attachments/assets/aa272b30-44dc-4da4-9314-2d7c2670c7c0" />



# Theory: 


Blynk is an IoT platform for iOS or Android smartphones that is used to control Arduino, Raspberry Pi and NodeMCU via the Internet. This application is used to create a graphical interface or human machine interface (HMI) by compiling and providing the appropriate address on the available widgets.In this experiment we use ESP8266 to control a 220-volt lamp from a web server. But you can also use the same procedure to control fans, lights, AC, or other electrical devices that you want to control remotely.
Relay is an electromechanical device that is used as a switch between high current and low current devices. When the coil in the relay gets fully energized, the contact shifts from the normally open position to the normally closed position. Light bulbs usually operate on 120V or 220V AC power supply. We cannot interface these AC loads directly with the ESP8266 development board, or it will damage the board. We have to use a relay between the ESP8266 and the lamp. 
Google Assistant and IFTTT work together to let you control services with voice commands. When you say a set phrase, Google Assistant processes it and sends it to IFTTT as a trigger. If the phrase matches an applet you've created, IFTTT performs the linked action—like turning on a light or sending a message. Everything runs in the cloud, making it easy to automate tasks with just your voice, as long as the command is correctly matched and all services are online.
When we apply an active high signal to the signal pin of the relay module from any microcontroller like ESP8266, the relay contact moves from the normally open to the normally closed position. It makes the circuit complete, and the output load turns on.


# Program:
```
// Define pins
#define CLAP_BUTTON_PIN 2       // Pin connected to the push button (clap simulation)
#define VOICE_LIGHT_ON_PIN 3    // Pin connected to Button1 (simulates "light on" voice command)
#define VOICE_LIGHT_OFF_PIN 4   // Pin connected to Button2 (simulates "light off" voice command)
#define VOICE_FAN_ON_PIN 5      // Pin connected to Button3 (simulates "fan on" voice command)
#define VOICE_FAN_OFF_PIN 6     // Pin connected to Button4 (simulates "fan off" voice command)
#define LED_PIN 13              // Pin connected to the LED (light)
#define RELAY_PIN 12            // Pin connected to the relay (fan)

bool lightState = false;          // Current state of the light
bool fanState = false;            // Current state of the fan
bool lastButtonState[5] = {HIGH}; // Previous states of all buttons
bool currentButtonState[5] = {HIGH}; // Current states of all buttons

unsigned long lastDebounceTime[5] = {0};  // Last time button states were checked
unsigned long debounceDelay = 50;    // Debounce time (ms)

void setup() {
  pinMode(CLAP_BUTTON_PIN, INPUT_PULLUP); // Set clap button pin as input with internal pull-up resistor
  pinMode(VOICE_LIGHT_ON_PIN, INPUT_PULLUP); // Set "light on" button pin as input with internal pull-up resistor
  pinMode(VOICE_LIGHT_OFF_PIN, INPUT_PULLUP); // Set "light off" button pin as input with internal pull-up resistor
  pinMode(VOICE_FAN_ON_PIN, INPUT_PULLUP); // Set "fan on" button pin as input with internal pull-up resistor
  pinMode(VOICE_FAN_OFF_PIN, INPUT_PULLUP); // Set "fan off" button pin as input with internal pull-up resistor
  pinMode(LED_PIN, OUTPUT);              // Set LED pin as output
  pinMode(RELAY_PIN, OUTPUT);            // Set relay pin as output

  digitalWrite(LED_PIN, LOW);            // Initialize LED state (OFF)
  digitalWrite(RELAY_PIN, HIGH);         // Initialize relay state (OFF) assuming relay is active-low
  Serial.begin(9600); // Start serial communication for debugging
}

void loop() {
  const int buttonPins[] = {CLAP_BUTTON_PIN, VOICE_LIGHT_ON_PIN, VOICE_LIGHT_OFF_PIN, VOICE_FAN_ON_PIN, VOICE_FAN_OFF_PIN};
  const char* buttonNames[] = {"Clap Button", "Voice Light On", "Voice Light Off", "Voice Fan On", "Voice Fan Off"};

  for (int i = 0; i < 5; i++) {
    int reading = digitalRead(buttonPins[i]);

    if (reading != lastButtonState[i]) {
      lastDebounceTime[i] = millis(); // Reset debounce timer
    }

    if ((millis() - lastDebounceTime[i]) > debounceDelay) {
      if (reading != currentButtonState[i]) {
        currentButtonState[i] = reading;

        if (currentButtonState[i] == LOW) {
          switch (i) {
            case 0: // Clap Button
              lightState = !lightState;
              fanState = !fanState;
              digitalWrite(LED_PIN, lightState ? HIGH : LOW);
              digitalWrite(RELAY_PIN, fanState ? LOW : HIGH);
              Serial.print(buttonNames[i]);
              Serial.print(" Pressed. Light: ");
              Serial.print(lightState);
              Serial.print(", Fan: ");
              Serial.println(fanState);
              break;
            case 1: // Voice Light On Button
              lightState = true;
              digitalWrite(LED_PIN, HIGH);
              Serial.println(buttonNames[i]);
              break;
            case 2: // Voice Light Off Button
              lightState = false;
              digitalWrite(LED_PIN, LOW);
              Serial.println(buttonNames[i]);
              break;
            case 3: // Voice Fan On Button
              fanState = true;
              digitalWrite(RELAY_PIN, LOW);
              Serial.println(buttonNames[i]);
              break;
            case 4: // Voice Fan Off Button
              fanState = false;
              digitalWrite(RELAY_PIN, HIGH);
              Serial.println(buttonNames[i]);
              break;
          }
        }
      }
    }

    lastButtonState[i] = reading;
  }
}
/*// Define pins
#define CLAP_BUTTON_PIN 2       // Pin connected to the push button (clap simulation)
#define VOICE_LIGHT_ON_PIN 3    // Pin connected to Button1 (simulates "light on" voice command)
#define VOICE_LIGHT_OFF_PIN 4   // Pin connected to Button2 (simulates "light off" voice command)
#define VOICE_FAN_ON_PIN 5      // Pin connected to Button3 (simulates "fan on" voice command)
#define VOICE_FAN_OFF_PIN 6     // Pin connected to Button4 (simulates "fan off" voice command)
#define LED_PIN 13              // Pin connected to the LED (light)
#define RELAY_PIN 12            // Pin connected to the relay (fan)

bool lightState = false;          // Current state of the light
bool fanState = false;            // Current state of the fan
bool lastClapButtonState = HIGH;  // Previous state of the clap button
bool lastVoiceLightOnState = HIGH; // Previous state of the "light on" button
bool lastVoiceLightOffState = HIGH; // Previous state of the "light off" button
bool lastVoiceFanOnState = HIGH;  // Previous state of the "fan on" button
bool lastVoiceFanOffState = HIGH; // Previous state of the "fan off" button
bool currentClapButtonState = HIGH; // Current state of the clap button
bool currentVoiceLightOnState = HIGH; // Current state of the "light on" button
bool currentVoiceLightOffState = HIGH; // Current state of the "light off" button
bool currentVoiceFanOnState = HIGH; // Current state of the "fan on" button
bool currentVoiceFanOffState = HIGH; // Current state of the "fan off" button

unsigned long lastDebounceTime = 0;  // Last time button state was checked
unsigned long debounceDelay = 50;    // Debounce time (ms)

void setup() {
  pinMode(CLAP_BUTTON_PIN, INPUT_PULLUP); // Set clap button pin as input with internal pull-up resistor
  pinMode(VOICE_LIGHT_ON_PIN, INPUT_PULLUP); // Set "light on" button pin as input with internal pull-up resistor
  pinMode(VOICE_LIGHT_OFF_PIN, INPUT_PULLUP); // Set "light off" button pin as input with internal pull-up resistor
  pinMode(VOICE_FAN_ON_PIN, INPUT_PULLUP); // Set "fan on" button pin as input with internal pull-up resistor
  pinMode(VOICE_FAN_OFF_PIN, INPUT_PULLUP); // Set "fan off" button pin as input with internal pull-up resistor
  pinMode(LED_PIN, OUTPUT);              // Set LED pin as output
  pinMode(RELAY_PIN, OUTPUT);            // Set relay pin as output

  digitalWrite(LED_PIN, LOW);            // Initialize LED state (OFF)
  digitalWrite(RELAY_PIN, HIGH);         // Initialize relay state (OFF) assuming relay is active-low
  Serial.begin(9600); // Start serial communication for debugging
}

void loop() {
  int readingClapButton = digitalRead(CLAP_BUTTON_PIN);
  int readingVoiceLightOn = digitalRead(VOICE_LIGHT_ON_PIN);
  int readingVoiceLightOff = digitalRead(VOICE_LIGHT_OFF_PIN);
  int readingVoiceFanOn = digitalRead(VOICE_FAN_ON_PIN);
  int readingVoiceFanOff = digitalRead(VOICE_FAN_OFF_PIN);

  // Handle Clap Button (Simulates Clap Detection)
  if (readingClapButton != lastClapButtonState) {
    lastDebounceTime = millis(); // Reset debounce timer
  }
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (readingClapButton != currentClapButtonState) {
      currentClapButtonState = readingClapButton;
      if (currentClapButtonState == LOW) {
        lightState = !lightState;  // Toggle light state
        fanState = !fanState;      // Toggle fan state
        digitalWrite(LED_PIN, lightState ? HIGH : LOW);
        digitalWrite(RELAY_PIN, fanState ? LOW : HIGH); // Relay control
        Serial.print("Clap Button Pressed. Light: ");
        Serial.print(lightState);
        Serial.print(", Fan: ");
        Serial.println(fanState);
      }
    }
  }

  lastClapButtonState = readingClapButton; // Save the current button state for comparison in the next loop

  // Handle Voice Light On Button
  if (readingVoiceLightOn != lastVoiceLightOnState) {
    lastDebounceTime = millis(); // Reset debounce timer
  }
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (readingVoiceLightOn != currentVoiceLightOnState) {
      currentVoiceLightOnState = readingVoiceLightOn;
      if (currentVoiceLightOnState == LOW) {
        lightState = true;  // Turn on the light
        digitalWrite(LED_PIN, HIGH);
        Serial.println("Voice Light On Button Pressed.");
      }
    }
  }

  lastVoiceLightOnState = readingVoiceLightOn; // Save the current button state for comparison in the next loop

  // Handle Voice Light Off Button
  if (readingVoiceLightOff != lastVoiceLightOffState) {
    lastDebounceTime = millis(); // Reset debounce timer
  }
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (readingVoiceLightOff != currentVoiceLightOffState) {
      currentVoiceLightOffState = readingVoiceLightOff;
      if (currentVoiceLightOffState == LOW) {
        lightState = false; // Turn off the light
        digitalWrite(LED_PIN, LOW);
        Serial.println("Voice Light Off Button Pressed.");
      }
    }
  }

  lastVoiceLightOffState = readingVoiceLightOff; // Save the current button state for comparison in the next loop

  // Handle Voice Fan On Button
  if (readingVoiceFanOn != lastVoiceFanOnState) {
    lastDebounceTime = millis(); // Reset debounce timer
  }
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (readingVoiceFanOn != currentVoiceFanOnState) {
      currentVoiceFanOnState = readingVoiceFanOn;
      if (currentVoiceFanOnState == LOW) {
        fanState = true;  // Turn on the fan
        digitalWrite(RELAY_PIN, LOW); // Relay control
        Serial.println("Voice Fan On Button Pressed.");
      }
    }
  }

  lastVoiceFanOnState = readingVoiceFanOn; // Save the current button state for comparison in the next loop

  // Handle Voice Fan Off Button
  if (readingVoiceFanOff != lastVoiceFanOffState) {
    lastDebounceTime = millis(); // Reset debounce timer
  }
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (readingVoiceFanOff != currentVoiceFanOffState) {
      currentVoiceFanOffState = readingVoiceFanOff;
      if (currentVoiceFanOffState == LOW) {
        fanState = false; // Turn off the fan
        digitalWrite(RELAY_PIN, HIGH); // Relay control
        Serial.println("Voice Fan Off Button Pressed.");
      }
    }
  }

  lastVoiceFanOffState = readingVoiceFanOff; // Save the current button state for comparison in the next loop
}*/
```


# Procedure:
•	Make the circuit connection as per the diagram. In the mobile, download and “Blynq IoT” application using Google play store and Install it. Create log in ID and Password.
•	Connect the IN pin of the Relay module to D1 pin of NodeMCU (ESP8266).
•	Connect VCC of the Relay of NodeMCU. Connect GND of the Relay to GND of NodeMCU. 
•	Connect your AC bulb to the Relay’s switch terminal securely.
•	Install ESP8266 board in Arduino IDE via Board Manager. Select board: NodeMCU 1.0 (ESP-12E Module).
•	Include necessary libraries: ESP8266WiFi and ESP8266WebServer.
•	In the code, configure Wi-Fi SSID and Password.
•	Set up a web server that responds to /on and /off URLs.
•	Upload the code to the ESP8266 using a micro USB cable.
•	Get Local IP Address After uploading, open Serial Monitor to find the local IP address of ESP8266.
•	Create Applets on IFTTT - For "This", select Google Assistant → "Say a simple phrase". Command: "Turn on the light". For "That", choose Webhooks → "Make a web request". 
•	Repeat to create another applet for command with URL.
•	Test the System - Google Assistant triggers IFTTT → sends Webhook to ESP8266 → turns ON the relay (light).
•	Say "Turn off the ligh to switch it OFF, Say "Turn on the light" to switch it ON.


# Output:
https://private-user-images.githubusercontent.com/208609781/500830619-593df445-0c7b-4926-ac4c-0e6b7d0c9813.mp4?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjMxODA3MTMsIm5iZiI6MTc2MzE4MDQxMywicGF0aCI6Ii8yMDg2MDk3ODEvNTAwODMwNjE5LTU5M2RmNDQ1LTBjN2ItNDkyNi1hYzRjLTBlNmI3ZDBjOTgxMy5tcDQ_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMTE1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTExNVQwNDIwMTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT02YjkyNjEyYjI5NmE1MmI4NWFjYzc4NzhkMzQzZDZiNDcwMjZmNTcyZTJkMTZlMjI4MjI2MjU0MWM0OGRhOTcwJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.8fP065Bsw_PYoV-PxznPY0uGfSwbkJ4eTBP_jDPr7T0


# Result:
https://private-user-images.githubusercontent.com/208609781/500830382-720c9034-ed98-46f5-a82e-c10f06c6a4d2.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjMxODA3MTMsIm5iZiI6MTc2MzE4MDQxMywicGF0aCI6Ii8yMDg2MDk3ODEvNTAwODMwMzgyLTcyMGM5MDM0LWVkOTgtNDZmNS1hODJlLWMxMGYwNmM2YTRkMi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMTE1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTExNVQwNDIwMTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1mYTQ3ZDM3MTU4ZjNhNGJlYWVlNTViZDZjMDFkODZjZGM3MzUwNzU3ZGUyZGJjMTQ1YTI2OGQzMGY5YTBhYTkxJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.WDD6a8zWvPLejbW652e5uoQr0tQ9AnbUjY7QhuyWJAs

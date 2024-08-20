# Automated-liquid-volume-measurement-and-filling-system#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <NewPing.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);  // Set the LCD address and dimensions

#define TRIGGER1 2
#define ECHO1 3
#define TRIGGER2 4
#define ECHO2 5
#define RELAY 6

NewPing sonar1(TRIGGER1, ECHO1);
NewPing sonar2(TRIGGER2, ECHO2);

float glassHeight = 5.0; // in cm
float liquidHeight, glassVolume, liquidVolume;
const float glassRadius = 6.0; // in cm
const float sensorHeight = 20.0; // in cm
float initialVolume;

void setup() {
  lcd.begin(16, 2);        // Initialize the LCD with 16 columns and 2 rows
  lcd.backlight();
  
  pinMode(RELAY, OUTPUT);   // Set the relay pin as an output
  digitalWrite(RELAY, HIGH); // Turn off the relay initially
}

void loop() {
  int distance1 = sonar1.ping_cm();
  int distance2 = sonar2.ping_cm();

  liquidHeight = sensorHeight - distance1;
  glassVolume = PI * pow(glassRadius, 2) * glassHeight;
  liquidVolume = PI * pow(glassRadius, 2) * liquidHeight;

  lcd.clear();  // Clear the LCD before printing new messages

  if (distance2 < 20) {
    lcd.print("GLASS DETECTED");
    lcd.setCursor(0, 1);
    if (liquidVolume < glassVolume) {
      digitalWrite(RELAY, LOW);    // Turn on the relay to activate the pump
      lcd.print("FILLING");
    } else {
      digitalWrite(RELAY, HIGH);   // Turn off the relay to deactivate the pump
      lcd.print("FILLED");
      delay(2000); // Delay for 2 seconds to display "FILLED" before clearing the screen
      lcd.clear(); // Clear the screen before displaying the volume filled
      lcd.setCursor(0, 0);
      lcd.print("VOLUME FILLED");
      lcd.setCursor(0, 1);
      lcd.print("= ");
      lcd.print(liquidVolume - initialVolume);
      delay(5000); // Display the volume for 5 seconds before resetting
      initialVolume = 0; // Reset the initial volume
    }
  } else {
    lcd.print("PLACE GLASS HERE");
    digitalWrite(RELAY, HIGH);     // Turn off the relay to deactivate the pump
  }
  delay(1000);
}
BRO CODE 1

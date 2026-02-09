# collage-project
automated medical waste segregation using arduino microcontroller

#include <LiquidCrystal.h>
#include <Servo.h>

// LCD configuration: RS=9, E=10, D4=11, D5=12, D6=13, D7=3
LiquidCrystal lcd(9, 10, 11, 12, 13, 3);
Servo myservo;

#define S0 4
#define S1 5
#define S2 6
#define S3 7
#define sensorOut 8
#define IR_SENSOR A0  // use A0 instead of pin 14 for clarity

int redFrequency = 0;
int greenFrequency = 0;
int blueFrequency = 0;

void setup() {
  // Set pin modes
  pinMode(S0, OUTPUT);
  pinMode(S1, OUTPUT);
  pinMode(S2, OUTPUT);
  pinMode(S3, OUTPUT);
  pinMode(sensorOut, INPUT);
  pinMode(IR_SENSOR, INPUT);

  // Configure color sensor
  digitalWrite(S0, HIGH);  // 100% scaling
  digitalWrite(S1, LOW);

  // Initialize peripherals
  Serial.begin(9600);
  lcd.begin(16, 2);
  myservo.attach(2);
  myservo.write(90);  // Default center position
}

void loop() {
  // Read color frequencies
  digitalWrite(S2, LOW); digitalWrite(S3, LOW);   // RED
  redFrequency = pulseIn(sensorOut, LOW);
  delay(100);

  digitalWrite(S2, HIGH); digitalWrite(S3, HIGH); // GREEN
  greenFrequency = pulseIn(sensorOut, LOW);
  delay(100);

  digitalWrite(S2, LOW); digitalWrite(S3, HIGH);  // BLUE
  blueFrequency = pulseIn(sensorOut, LOW);
  delay(100);

  // Display color values
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("R:"); lcd.print(redFrequency);
  lcd.print(" G:"); lcd.print(greenFrequency);

  lcd.setCursor(0, 1);
  lcd.print("B:"); lcd.print(blueFrequency);

  // Read IR sensor
  int ir = digitalRead(IR_SENSOR);
  lcd.setCursor(11, 1); lcd.print("IR:"); lcd.print(ir);

  // Debug to Serial
  Serial.print("R = "); Serial.print(redFrequency);
  Serial.print(" G = "); Serial.print(greenFrequency);
  Serial.print(" B = "); Serial.println(blueFrequency);

  // Servo control based on color thresholds
  // Adjust these thresholds based on testing
  if (redFrequency < 300) {
    myservo.write(0);  // Red bin
    Serial.println("Red Detected - Servo to 0°");
  } else if (greenFrequency < 300) {
    myservo.write(90); // Green bin
    Serial.println("Green Detected - Servo to 90°");
  } else if (blueFrequency < 300) {
    myservo.write(180); // Blue bin
    Serial.println("Blue Detected - Servo to 180°");
  } else {
    myservo.write(45); // Default/Unknown
    Serial.println("No clear color - Servo to 45°");
  }

  delay(2000); // Wait before next detection
}

convert this into python



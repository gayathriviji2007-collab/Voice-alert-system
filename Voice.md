#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// Pin Definitions
const int irSensorPin = 2;
const int isd1820PlayPin = 3;
const int isd1820RecPin = 4;

// LCD Initialization (I2C address 0x27)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// OLED display dimensions and address
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 oled(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

void setup() {
  // Pin modes
  pinMode(irSensorPin, INPUT);
  pinMode(isd1820PlayPin, OUTPUT);
  pinMode(isd1820RecPin, OUTPUT);

  digitalWrite(isd1820PlayPin, LOW);
  digitalWrite(isd1820RecPin, LOW);

  Serial.begin(9600);
  Serial.println("Customizable Alarm System Initialized");

  // LCD Initialization
  lcd.init();
  lcd.backlight();
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("System Ready");
  lcd.setCursor(0, 1);
  lcd.print("Monitoring...");

  // OLED Initialization
  if (!oled.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;); // Halt
  }

  oled.clearDisplay();
  oled.setTextSize(1);
  oled.setTextColor(WHITE);
  oled.setCursor(0, 0);
  oled.println("System Ready");
  oled.setCursor(0, 10);
  oled.println("Monitoring...");
  oled.display();
}

void loop() {
  int irSensorState = digitalRead(irSensorPin);

  if (irSensorState == HIGH) {
    Serial.println("Motion detected! Playing custom alarm...");

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("** WARNING **");
    lcd.setCursor(0, 1);
    lcd.print("Restricted Area");

    oled.clearDisplay();
    oled.setCursor(0, 0);
    oled.setTextSize(2);
    oled.println("WARNING!");
    oled.setTextSize(1);
    oled.setCursor(0, 30);
    oled.println("Restricted Area");
    oled.display();

    playAlarm();
  }

  delay(200); // Delay for stability
}

void recordAlarm() {
  Serial.println("Recording started. Speak now...");

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Recording...");
  lcd.setCursor(0, 1);
  lcd.print("Speak now");

  oled.clearDisplay();
  oled.setTextSize(1);
  oled.setCursor(0, 0);
  oled.println("Recording...");
  oled.setCursor(0, 10);
  oled.println("Speak Now");
  oled.display();

  digitalWrite(isd1820RecPin, HIGH);
  delay(10000);
  digitalWrite(isd1820RecPin, LOW);

  Serial.println("Recording complete.");
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Recording Done");

  oled.clearDisplay();.
  oled.setCursor(0, 0);
  oled.println("Recording Done");
  oled.display();

  delay(2000);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("System Ready");
  lcd.setCursor(0, 1);
  lcd.print("Monitoring...");

  oled.clearDisplay();
  oled.setCursor(0, 0);
  oled.println("System Ready");
  oled.setCursor(0, 10);
  oled.println("Monitoring...");
  oled.display();
}

void playAlarm() {
  digitalWrite(isd1820PlayPin, HIGH);
  delay(1000); // Adjust to alarm length
  digitalWrite(isd1820PlayPin, LOW);

  delay(2000);

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("System Ready");
  lcd.setCursor(0, 1);
  lcd.print("Monitoring...");

  oled.clearDisplay();
  oled.setCursor(0, 0);
  oled.setTextSize(1);
  oled.println("System Ready");
  oled.setCursor(0, 10);
  oled.println("Monitoring...");
  oled.display();
}

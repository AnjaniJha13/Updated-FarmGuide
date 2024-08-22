// Updated-FarmGuide
#define BLYNK_PRINT Serial
#define BLYNK_TEMPLATE_ID "TMPL3ra5Gk2IH"
#define BLYNK_TEMPLATE_NAME "testing"
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

// Blynk Auth Token
char auth[] = "emWKWNMZNt0impqcF6c7O7Q3TjoTwTRq";

// WiFi credentials
char ssid[] = "realme";
char pass[] = "nfr9393z";

// DHT11 settings
#define DHTPIN D2
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

// Virtual pins
#define VPIN_SOIL1 V1
#define VPIN_SOIL2 V2
#define VPIN_TEMP V3
#define VPIN_HUM V4
#define VPIN_PIR V5

// Sensor pins
#define SOIL1_PIN A0 // Use A0 for analog reading
#define SOIL2_PIN A0 // Use A0 for analog reading with multiplexer or switch
#define PIR_PIN D3

void setup() {
  // Debug console
  Serial.begin(9600);

  // Blynk setup
  Blynk.begin(auth, ssid, pass);

  // DHT sensor setup
  dht.begin();

  // PIR sensor setup
  pinMode(PIR_PIN, INPUT);

  // Soil moisture sensor setup
  pinMode(SOIL1_PIN, INPUT);
  pinMode(SOIL2_PIN, INPUT);
}

void loop() {
  Blynk.run();

  // Read soil moisture sensor 1
  int soil1 = analogRead(SOIL1_PIN);
  int value1 = map(soil1, 0, 1024, 0, 100);
  value1 = (value1 - 100) * -1;

  Blynk.virtualWrite(VPIN_SOIL1, value1);
  Serial.print("Soil Moisture 1: ");
  Serial.print(value1);
  Serial.println(" %");

  // Read soil moisture sensor 2
  int soil2 = analogRead(SOIL2_PIN);
  int value2 = map(soil2, 0, 1024, 0, 100);
  value2 = (value2 - 100) * -1;

  Blynk.virtualWrite(VPIN_SOIL2, value2);
  Serial.print("Soil Moisture 2: ");
  Serial.print(value2);
  Serial.println(" %");

  // Read DHT11 sensor
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  Serial.print("Temperature: ");
  Serial.println(t);
  Serial.print("Humidity: ");
  Serial.println(h);
  Blynk.virtualWrite(VPIN_TEMP, t);
  Blynk.virtualWrite(VPIN_HUM, h);

  // Read PIR sensor
  int pirState = digitalRead(PIR_PIN);
  Serial.print("PIR State: ");
  Serial.println(pirState);
  Blynk.virtualWrite(VPIN_PIR, pirState);

  delay(2000); // Delay between readings
}

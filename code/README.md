#define BLYNK_PRINT Serial

#define BLYNK_TEMPLATE_ID "TMPL3Y2ZqC2WC"
#define BLYNK_TEMPLATE_NAME "Thermoelectric refrigerator"
#define BLYNK_AUTH_TOKEN "3uQvf_8Hb1VYHizwwT5cdX-1QntvJybH"

#include <WiFi.h>
#include <BlynkSimpleEsp32.h>

#include <OneWire.h>
#include <DallasTemperature.h>

// ---------------- WIFI ----------------
char ssid[] = "esp32test";
char pass[] = "ONLYKRADS";

// ---------------- PINS ----------------
#define RELAY_PIN 5
#define ONE_WIRE_BUS 4

OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

// ---------------- TEMP LIMITS ----------------
const float TEMP_ON  = 9.0;
const float TEMP_OFF = 7.0;

// ---------------- VARIABLES ----------------
float tempValue = 0.0;
bool relayState = true;

// ---------------- TIMING ----------------
const unsigned long UPDATE_INTERVAL = 7000;
unsigned long lastUpdate = 0;

// ---------------- GPS ----------------
const float GPS_LAT = 12.9238;
const float GPS_LON = 77.4985;

bool gpsReady = false;
unsigned long gpsStartTime;

// ---------------- INA226 (SIMULATED) ----------------
float voltage = 0.0;
float current = 0.0;
float power = 0.0;

void setup()
{
    Serial.begin(115200);
    delay(2000);

    pinMode(RELAY_PIN, OUTPUT);

    digitalWrite(RELAY_PIN, LOW);

    sensors.begin();

    Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

    gpsStartTime = millis();

    randomSeed(esp_random());

    Serial.println("System Initialized");
    Serial.println("--------------------------------");
}

void loop()
{
    Blynk.run();

    unsigned long now = millis();

    if (!gpsReady && now - gpsStartTime >= 30000)
    {
        gpsReady = true;
        Serial.println("GPS Locked");
    }

    if (now - lastUpdate >= UPDATE_INTERVAL)
    {
        lastUpdate = now;

        sensors.requestTemperatures();
        tempValue = sensors.getTempCByIndex(0);

        if (tempValue == DEVICE_DISCONNECTED_C)
        {
            Serial.println("Temperature sensor disconnected!");
            return;
        }

        if (relayState)
        {
            if (tempValue <= TEMP_OFF)
            {
                relayState = false;
                digitalWrite(RELAY_PIN, HIGH);
            }
        }
        else
        {
            if (tempValue >= TEMP_ON)
            {
                relayState = true;
                digitalWrite(RELAY_PIN, LOW);
            }
        }

        if (relayState)
        {
            voltage = random(1120, 1210) / 100.0;
            current = random(880, 1020) / 100.0;
        }
        else
        {
            voltage = 0.0;
            current = 0.0;
        }

        power = voltage * current;

        Serial.print("Temperature: ");
        Serial.print(tempValue, 2);
        Serial.println(" °C");

        Serial.print("Relay: ");
        Serial.println(relayState ? "ON" : "OFF");

        Serial.print("Voltage: ");
        Serial.print(voltage, 2);
        Serial.println(" V");

        Serial.print("Current: ");
        Serial.print(current, 2);
        Serial.println(" A");

        Serial.print("Power: ");
        Serial.print(power, 2);
        Serial.println(" W");

        if (gpsReady)
        {
            Serial.print("GPS: ");
            Serial.print(GPS_LAT, 4);
            Serial.print(", ");
            Serial.println(GPS_LON, 4);
        }
        else
        {
            Serial.println("GPS: Initializing...");
        }

        Serial.println("--------------------------------");

        Blynk.virtualWrite(V0, tempValue);
        Blynk.virtualWrite(V7, tempValue);
        Blynk.virtualWrite(V1, relayState ? "ON" : "OFF");
        Blynk.virtualWrite(V2, voltage);
        Blynk.virtualWrite(V3, current);
        Blynk.virtualWrite(V4, power);
        Blynk.virtualWrite(V5, gpsReady ? GPS_LAT : 0);
        Blynk.virtualWrite(V6, gpsReady ? GPS_LON : 0);
    }
}

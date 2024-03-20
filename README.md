#include <SPI.h>
#include <SD.h>
#include <Wire.h>
#include <BH1750.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

#define SD_CS_PIN D8 // Pino de chip select do cartão SD

File dataFile;

BH1750 lightMeter;
Adafruit_BME280 bme;

#define SEALEVELPRESSURE_HPA (1013.25)

const int pinSensorUV = A0;

void setup() {
  Serial.begin(9600);
  
  // Inicialização do cartão SD
  if (!SD.begin(SD_CS_PIN)) {
    Serial.println("Falha ao inicializar o cartão SD");
    return;
  } else {
    Serial.println("Cartão OK");
  }

  // Inicialização do sensor BH1750
  Wire.begin();
  lightMeter.begin();
  Serial.println(F("BH1750 Test begin"));

  // Inicialização do sensor BME280
  bool status = bme.begin(0x76); // endereço padrão
  if (!status) {
    Serial.println("Could not find a valid BME280 sensor, check wiring!");
    while (1);
  }
  Serial.println("BME280 Test begin");
}

void loop() {
  // Leitura do sensor de luz BH1750
  float lux = lightMeter.readLightLevel();
  Serial.print("Luminosidade: ");
  Serial.print(lux);
  Serial.println(" lx");

  // Leitura do sensor BME280
  Serial.print("Temperatura: ");
  Serial.print(bme.readTemperature());
  Serial.println(" °C");
  Serial.print("Pressão: ");
  Serial.print(bme.readPressure() / 100.0F);
  Serial.println(" hPa");
  Serial.print("Altitude: ");
  Serial.print(bme.readAltitude(SEALEVELPRESSURE_HPA));
  Serial.println(" m");
  Serial.print("Umidade: ");
  Serial.print(bme.readHumidity());
  Serial.println(" %");

  // Leitura do sensor UV
  int sensorValue = analogRead(pinSensorUV);
  float voltage = sensorValue * (3.3 / 1023.0);
  float uvIntensity = voltage * 307.0; // fator de calibração
  Serial.print("Intensidade UV: ");
  Serial.print(uvIntensity);
  Serial.println(" mW/cm^2");

  delay(1000); // Aguarda 1 segundo antes da próxima leitura
}

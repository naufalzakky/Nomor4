
<h1 align="center">Laporan Praktikum Sistem Embedded</h1>
<p>Disusun Oleh:</p>

<ul>
  <li>Naufal Faterna Zakky 4.31.20.0.17</li>
</ul>

<h3>Daftar Jobsheet</h3>
<p></p>

<ul>
  <li><a href="https://github.com/naufalzakky/JobSheet1">JobSheet1</a></li>
  <li><a href="https://github.com/naufalzakky/JobSheet1.1">JobSheet1.1</a></li>
  <li><a href="https://github.com/naufalzakky/JobSheet2">JobSheet2</a></li>
  <li><a href="https://github.com/naufalzakky/JobSheet3">JobSheet3</a></li>
  <li><a href="https://github.com/naufalzakky/Nomor1">Nomor 1</a></li>
  <li><a href="https://github.com/naufalzakky/Nomor2">Nomor 2</a></li>
  <li><a href="https://github.com/naufalzakky/Nomor3">Nomor 3</a></li>
  <li><a href="https://github.com/naufalzakky/Nomor4">Nomor 4</a></li>
</ul>


<h2 align="center">Tugas Nomor4</h2>
<h3>Coding</h3>
<p>ESP NOW+IOT</p>
<pre>
<code>
#include <esp_now.h>
#include <WiFi.h>
#include "DHT.h"
#define DHTPIN 4
#define DHTTYPE DHT11

uint8_t broadcastAddress[] = {0x94, 0xB5, 0x55, 0x2C, 0x47, 0xB4};
const int espNumber = 1;

typedef struct struct_message {
  int id;
  float temp;
  float hum;
} struct_message;
struct_message myData;
esp_now_peer_info_t peerInfo;

void OnDataSent(const uint8_t *mac_addr, esp_now_send_status_t status) {
  Serial.print("\r\nLast Packet Send Status:\t");
  Serial.println(status == ESP_NOW_SEND_SUCCESS ? "Delivery Success" : "Delivery Fail");
}

// Variable to hold readings
float temperature;
float humidity;

WiFiClient  client;
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.mode(WIFI_STA);
  if (esp_now_init() != ESP_OK) {
    Serial.println("Error initializing ESP-NOW");
    return;
  }
  esp_now_register_send_cb(OnDataSent);

  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = false;

  if (esp_now_add_peer(&peerInfo) != ESP_OK) {
    Serial.println("Failed to add peer");
    return;
  }
}

void loop() {
  humidity = dht.readHumidity();
  temperature = dht.readTemperature();
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  Serial.print("Temperature (ºC): ");
  Serial.print(temperature);
  Serial.print(" Humidity (%): ");
  Serial.println(humidity);

  myData.id = espNumber;
  myData.temp = temperature;
  myData.hum = humidity;
  esp_err_t result = esp_now_send(broadcastAddress, (uint8_t *) &myData, sizeof(myData));

  if (result == ESP_OK) {
    Serial.println("Sent with success");
  }
  else {
    Serial.println("Error sending the data");
  }

  delay(1000);
}

</code>
</pre>

<h3>Hasil</h3>

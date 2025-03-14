# IoT Enabled Air Pollution Monitoring Meter

This project is an IoT-enabled air pollution monitoring meter that uses an Arduino board and an MQ135 gas sensor to measure air quality. The data is then sent to a cloud platform for monitoring and analysis.

## Components
- Arduino Uno
- MQ135 Gas Sensor
- ESP8266 Wi-Fi Module
- Breadboard and Jumper Wires
- USB Cable

## Circuit Diagram
![Circuit Diagram](circuit-diagram.png)

## Code

```cpp name=air_pollution_monitor.ino
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
const char* serverName = "http://your_server_endpoint";

int mq135Pin = A0;

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  
  Serial.println("Connected to WiFi");
}

void loop() {
  int airQuality = analogRead(mq135Pin);
  Serial.print("Air Quality: ");
  Serial.println(airQuality);

  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(serverName);

    http.addHeader("Content-Type", "application/x-www-form-urlencoded");
    String httpRequestData = "airQuality=" + String(airQuality);
    int httpResponseCode = http.POST(httpRequestData);

    if (httpResponseCode > 0) {
      String response = http.getString();
      Serial.println(httpResponseCode);
      Serial.println(response);
    } else {
      Serial.print("Error on sending POST: ");
      Serial.println(httpResponseCode);
    }

    http.end();
  } else {
    Serial.println("Error in WiFi connection");
  }

  delay(60000); // Send data every 60 seconds
}
```

## Instructions
1. Connect the components as shown in the circuit diagram.
2. Replace `your_SSID`, `your_PASSWORD`, and `your_server_endpoint` with your Wi-Fi credentials and server endpoint.
3. Upload the code to the Arduino board using the Arduino IDE.
4. Open the Serial Monitor to view the air quality readings and the status of the data transmission.

## Cloud Platform
You can use any cloud platform of your choice to store and visualize the data. For example, you can use Thingspeak, Firebase, or a custom server.

## Notes
- Make sure to calibrate the MQ135 sensor for accurate readings.
- You may need additional components like resistors and capacitors depending on your circuit design.
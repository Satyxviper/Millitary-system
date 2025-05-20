# Millitary-system
**#arduino ide code**
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "TendaX"; // Your WiFi SSID
const char* password = "220@4321"; // Your WiFi Password

WebServer server(80);

// Ultrasonic Sensor Pins
const int trigPins[] = {5, 18, 19};
const int echoPins[] = {21, 22, 23};
const int numUltrasonic = 3;

// PIR and Microphone Sensor Pins
const int pirPin = 4;
const int micA0 = 35;
const int micD0 = 32;

// Function to measure distance
long readDistance(int trigPin, int echoPin) {
digitalWrite(trigPin, LOW);
delayMicroseconds(2);
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);
return pulseIn(echoPin, HIGH) * 0.034 / 2;
}

// Webpage with Advanced UI
String htmlPage() {
String html = R"rawliteral(

<title>Military Surveillance</title> <style> body { font-family: Arial, sans-serif; background-color: #1a1a1a; color: #ffffff; text-align: center; } h1 { color: #4CAF50; } .container { width: 80%; margin: auto; } .card { background: #333; padding: 20px; margin: 10px; border-radius: 10px; box-shadow: 2px 2px 10px rgba(0,0,0,0.5); } .status { font-size: 24px; font-weight: bold; } .danger { color: red; } .safe { color: green; } .grid { display: flex; justify-content: center; flex-wrap: wrap; } </style> <script> function updateData() { fetch("/data").then(response => response.json()).then(data => { document.getElementById("dist1").innerText = data.dist1 + " cm"; document.getElementById("dist2").innerText = data.dist2 + " cm"; document.getElementById("dist3").innerText = data.dist3 + " cm"; document.getElementById("pir").innerText = data.pir ? "⚠️ Enemy Detected!" : "✅ Area Secure"; document.getElementById("pir").className = data.pir ? "status danger" : "status safe"; document.getElementById("micA0").innerText = "Sound Level: " + data.micA0; document.getElementById("micD0").innerText = data.micD0 ? "⚠️ Gunshots Detected!" : "✅ No Sound"; document.getElementById("micD0").className = data.micD0 ? "status danger" : "status safe"; }); } setInterval(updateData, 2000); </script>
🔰 Military Surveillance System
Enemy Distance (Sensor 1)
Loading...

Enemy Distance (Sensor 2)
Loading...

Enemy Distance (Sensor 3)
Loading...

Enemy Movement
Loading...

Sound Level
Loading...

Gunshots Detected
Loading...

)rawliteral";
return html;
}

// Send Sensor Data as JSON
void handleData() {
String json = "{";
json += ""dist1":" + String(readDistance(trigPins[0], echoPins[0])) + ",";
json += ""dist2":" + String(readDistance(trigPins[1], echoPins[1])) + ",";
json += ""dist3":" + String(readDistance(trigPins[2], echoPins[2])) + ",";
json += ""pir":" + String(digitalRead(pirPin)) + ",";
json += ""micA0":" + String(analogRead(micA0)) + ",";
json += ""micD0":" + String(digitalRead(micD0));
json += "}";
server.send(200, "application/json", json);
}

// Handle root webpage request
void handleRoot() {
server.send(200, "text/html", htmlPage());
}

void setup() {
Serial.begin(115200);

// Initialize Ultrasonic Sensors
for (int i = 0; i < numUltrasonic; i++) {
pinMode(trigPins[i], OUTPUT);
pinMode(echoPins[i], INPUT);
}

pinMode(pirPin, INPUT);
pinMode(micD0, INPUT);

// Connect to WiFi
WiFi.begin(ssid, password);
Serial.print("Connecting to WiFi");
while (WiFi.status() != WL_CONNECTED) {
delay(500);
Serial.print(".");
}

Serial.println("\nWiFi Connected!");
Serial.print("Access the web interface at: http://");
Serial.println(WiFi.localIP());

// Start the Web Server
server.on("/", handleRoot);
server.on("/data", handleData);
server.begin();
}

void loop() {
server.handleClient();
}
**Screenshots**
![WhatsApp Image 2025-05-20 at 15 43 58_c698a750](https://github.com/user-attachments/assets/f28140ee-f227-42cb-8eb4-c39b0a06ca1a)
![WhatsApp Image 2025-05-20 at 15 43 48_fdf18de3](https://github.com/user-attachments/assets/63c0ffca-7360-4511-8b9b-8ef65d0225cb)
![WhatsApp Image 2025-05-20 at 15 43 32_dfa41a5f](https://github.com/user-attachments/assets/fe2aed6a-f387-4525-a258-4323feec7471)



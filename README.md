# PROYECTO-BORRADOR

## CODIGO
```
#include <ArduinoJson.h>
#include <WiFi.h>
#include <PubSubClient.h>
#define BUILTIN_LED 2
#include "DHTesp.h"
const int DHT_PIN = 15;
DHTesp dhtSensor;
// Update these with values suitable for your network.

#include <Stepper.h>

int RPM;
int Tempfalse;

const int stepsPerRevolution = 5000;

Stepper stepper1(stepsPerRevolution, 2, 4, 16,18);
Stepper stepper2(stepsPerRevolution, 22, 23, 19, 21);
Stepper stepper3(stepsPerRevolution, 17, 5, 13, 25);
Stepper stepper4(stepsPerRevolution, 33, 32, 34, 35);

const int led1 = 14;
const int led2 = 12;
const int led3 = 26;
const int led4 = 27;

const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "35.172.255.228";
String username_mqtt="AntonioM";
String password_mqtt="12345678";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);

pinMode(led1, OUTPUT);
pinMode(led2, OUTPUT);
pinMode(led3, OUTPUT);
pinMode(led4, OUTPUT);

stepper1.setSpeed(1);
stepper2.setSpeed(2);
stepper3.setSpeed(3);
stepper4.setSpeed(5);

}

void loop() {


delay(1000);
TempAndHumidity  data = dhtSensor.getTempAndHumidity();

RPM=(data.temperature*50);
Tempfalse=(data.temperature*6);

if (data.temperature>=14 && data.temperature<=20)
{
  digitalWrite(led1, HIGH);
  digitalWrite(led2, LOW);
  digitalWrite(led3, LOW);
  digitalWrite(led4, LOW);

stepper1.step(stepsPerRevolution);


  delay (2000);
  
}
else if (data.temperature>=34 && data.temperature<=40)
{
  digitalWrite(led1, HIGH);
  digitalWrite(led2, HIGH);
  digitalWrite(led3, LOW);
  digitalWrite(led4, LOW);

stepper2.step(stepsPerRevolution);


  delay (2000);
  
}
else if(data.temperature>=54 && data.temperature<=60) 
{
  digitalWrite(led1, HIGH);
  digitalWrite(led2, HIGH);
  digitalWrite(led3, HIGH);
  digitalWrite(led4, LOW);
 
stepper3.step(stepsPerRevolution);


  delay (2000);
  
  }
  else if(data.temperature>=70 && data.temperature<=80) 
{
  digitalWrite(led1, HIGH);
  digitalWrite(led2, HIGH);
  digitalWrite(led3, HIGH);
  digitalWrite(led4, HIGH);

stepper4.step(stepsPerRevolution);

  delay (2000);
 
  }
else
{
 digitalWrite(led1,  LOW);
  digitalWrite(led2, LOW);
  digitalWrite(led3, LOW);
  digitalWrite(led4, LOW);
}

  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "ESP32";
    //doc["Anho"] = 2022;
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = String(Tempfalse) + "°C";
    doc["RPM"] = String(RPM);
    doc["NOMBRE"]="ANTONIO DE JESUS MH";
   

    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    Serial.println(output.c_str());
    client.publish("PRACTICA1", output.c_str());
  }
}
```
## LIBRERIAS
![](https://github.com/AntoniodeJesus19/PROYECTO-BORRADOR/blob/main/Captura%20de%20pantalla%202024-12-15%20164711.png?raw=true)
## CONEXIONES
![](https://github.com/AntoniodeJesus19/PROYECTO-BORRADOR/blob/main/Captura%20de%20pantalla%202024-12-15%20164942.png?raw=true)
![](https://github.com/AntoniodeJesus19/PROYECTO-BORRADOR/blob/main/Captura%20de%20pantalla%202024-12-15%20165015.png?raw=true)
![](https://github.com/AntoniodeJesus19/PROYECTO-BORRADOR/blob/main/Captura%20de%20pantalla%202024-12-15%20165037.png?raw=true)
![](https://github.com/AntoniodeJesus19/PROYECTO-BORRADOR/blob/main/Captura%20de%20pantalla%202024-12-15%20165056.png?raw=true)
![]()

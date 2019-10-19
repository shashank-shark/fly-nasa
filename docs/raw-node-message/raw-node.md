# Getting raw TCP / IP messages from NodeMCU to our ZMQ.

The server written in python is shown below.

```python
import zmq

context = zmq.Context()

router = context.socket (zmq.ROUTER)
router.router_raw = True

router.bind ('tcp://*:8080')

while True:
    msg = router.recv_multipart()
    identity, body = msg

    print (identity)
    print (body)
```

At the end, we have NodeMCU which sends sensor related data to the server at regular intervals.

```cpp
#include <ESP8266WiFi.h>

#ifndef STASSID
#define STASSID "GodFromUniverse7"
#define STAPSK  "lordvilgax"
#endif

const char* ssid     = STASSID;
const char* password = STAPSK;

const char* host = "192.168.43.115";
const uint16_t port = 8080;

void setup() {
  Serial.begin(115200);

  // We start by connecting to a WiFi network

  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  /* Explicitly set the ESP8266 to be a WiFi-client, otherwise, it by default,
     would try to act as both a client and an access-point and could cause
     network-issues with your other WiFi-devices on your WiFi-network. */
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  Serial.print("connecting to ");
  Serial.print(host);
  Serial.print(':');
  Serial.println(port);

  // Use WiFiClient class to create TCP connections
  WiFiClient client;
  if (!client.connect(host, port)) {
    Serial.println("connection failed");
    delay(5000);
    return;
  }

  // This will send a string to the server
  Serial.println("sending data to server");
  if (client.connected()) {
    client.println("hello from ESP8266");
  }

  // wait for data to be available
  unsigned long timeout = millis();
  while (client.available() == 0) {
    if (millis() - timeout > 5000) {
      Serial.println(">>> Client Timeout !");
      client.stop();
      delay(60000);
      return;
    }
  }

  // Read all the lines of the reply from server and print them to Serial
  Serial.println("receiving from remote server");
  // not testing 'client.connected()' since we do not need to send data here
  while (client.available()) {
    char ch = static_cast<char>(client.read());
    Serial.print(ch);
  }

  // Close the connection
  Serial.println();
  Serial.println("closing connection");
  client.stop();

  delay(50000); // execute once every 5 minutes, don't flood remote service
}
```
#include <SPI.h>
#include <Ethernet.h>

// Configuração da Ethernet Shield
byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED }; // Endereço MAC da Ethernet Shield
IPAddress ip(192, 168, 1, 177); // Endereço IP do Arduino
EthernetServer server(80); // Porta do servidor web (80 é o padrão para HTTP)

// Pino do sensor de proximidade
const int sensorPin = A0;

void setup() {
  // Inicializa o sensor de proximidade como entrada
  pinMode(sensorPin, INPUT);

  // Inicializa a comunicação serial
  Serial.begin(9600);

  // Inicializa a Ethernet Shield
  Ethernet.begin(mac, ip);
  server.begin();
  
  Serial.print("Servidor web iniciado. Endereço IP: ");
  Serial.println(Ethernet.localIP());
}

void loop() {
  EthernetClient client = server.available();
  
  if (client) {
    Serial.println("Cliente conectado");

    // Lê o valor do sensor de proximidade
    int sensorValue = analogRead(sensorPin);
    
    // Constrói a resposta HTTP
    String response = "HTTP/1.1 200 OK\r\n";
    response += "Content-Type: text/html\r\n\r\n";
    response += "<!DOCTYPE HTML>\r\n";
    response += "<html>\r\n";
    response += "<h1>Valor do Sensor de Proximidade:</h1>\r\n";
    response += "<p>";
    response += sensorValue;
    response += "</p>\r\n";
    response += "</html>\r\n";
    
    // Envia a resposta ao cliente
    client.print(response);
    delay(1);
    client.stop();
    Serial.println("Cliente desconectado");
  }
}

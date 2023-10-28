#include <SoftwareSerial.h>

// Define GSM module connections
#define GSM_TX 7
#define GSM_RX 8

// Define gas sensor pin
#define GAS_SENSOR A0

// Threshold for gas detection
int gasThreshold = 200;

// Initialize SoftwareSerial for communication with GSM module
SoftwareSerial gsmSerial(GSM_TX, GSM_RX);

void setup() {
  Serial.begin(9600);
  gsmSerial.begin(9600);

  pinMode(GAS_SENSOR, INPUT);
}

void loop() {
  int gasValue = analogRead(GAS_SENSOR);

  Serial.print("Gas Value: ");
  Serial.println(gasValue);

  if (gasValue > gasThreshold) {
    sendSMS("Gas Leakage Detected!");
    delay(5000);  // Wait for a few seconds to avoid continuous alerts
  }

  delay(1000);  // Adjust the delay based on your requirements
}

void sendSMS(String message) {
  gsmSerial.println("AT+CMGF=1");  // Set SMS mode to text
  delay(1000);
  gsmSerial.println("AT+CMGS=\"+1234567890\"");  // Replace with the recipient's phone number
  delay(1000);
  gsmSerial.println(message);
  delay(1000);
  gsmSerial.println((char)26);  // Ctrl+Z to end the message
  delay(1000);
}

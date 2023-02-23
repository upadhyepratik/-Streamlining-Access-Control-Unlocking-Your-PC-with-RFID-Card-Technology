# -Streamlining-Access-Control-Unlocking-Your-PC-with-RFID-Card-Technology
#include <SPI.h>
#include <MFRC522.h>
#include <Keyboard.h>

#define RST_PIN         9           // Configurable, see typical pin layout above
#define SS_PIN          10          // Configurable, see typical pin layout above

MFRC522 mfrc522(SS_PIN, RST_PIN);   // Create MFRC522 instance.

String read_rfid;                   // Add how many you need and don't forget to include the UID.
String ok_rfid_1 = "e5e1a420";      // This is for my main RFID Card. aka. The one I will be using to turn on my PC. Can also be used to shut it down if you want to.
String ok_rfid_2 = "7d5d3e0";      // This is for the RFID Keyfob. aka. Shutdown Keyfob. Not advisable tho. Just shutdown your PC normally.
String ok_rfid_3 = "51601d69";      // This is for my main RFID Card. aka. The one I will be using to turn on my PC. Can also be used to shut it down if you want to.
String ok_rfid_4 = "d1732669";      // This is for my main RFID Card. aka. The one I will be using to turn on my PC. Can also be used to shut it down if you want to.

int lock = 2;                       // For the Card.
int lock2 = 3;                      // For the Keyfob.
int a = 0;
int b = 0;
int c = 0;
int d = 0;
/*
   Initialize.
*/
void setup() {
  Serial.begin(9600);         // Initialize serial communications with the PC
  while (!Serial);            // Do nothing if no serial port is opened (added for Arduinos based on ATMEGA32U4)
  SPI.begin();                // Init SPI bus
  mfrc522.PCD_Init();         // Init MFRC522 card

  //Choose which lock below:
  pinMode(lock, OUTPUT);
  pinMode(lock2, OUTPUT);
    digitalWrite(lock, HIGH);
  digitalWrite(lock2, HIGH);
}
/*
   Helper routine to dump a byte array as hex values to Serial.
*/
void dump_byte_array(byte *buffer, byte bufferSize) {
  read_rfid = "";
  for (byte i = 0; i < bufferSize; i++) {
    read_rfid = read_rfid + String(buffer[i], HEX);
  }
}

//void open_lock() {
//  //Use this routine when working with Relays and Solenoids etc.
//  digitalWrite(lock, HIGH);
//  digitalWrite(lock2, HIGH);
//  Serial.println("open relay");
//  a++;
//}
//
//
//void close_lock() {          // You can also just use the card to shutdown your PC. This is just for those moments that you really need to shut it down quickly.
//  //Use this routine when working with Relays and Solenoids etc.
//  digitalWrite(lock, LOW);
//  digitalWrite(lock2, LOW);
//  Serial.println("close relay");
//  a = 0;
//}


void loop() {

  // Look for new cards
  if ( ! mfrc522.PICC_IsNewCardPresent())
    return;

  // Select one of the cards
  if ( ! mfrc522.PICC_ReadCardSerial())
    return;

  dump_byte_array(mfrc522.uid.uidByte, mfrc522.uid.size);
  Serial.println(read_rfid);

  if ((read_rfid == ok_rfid_1 || read_rfid == ok_rfid_2 || read_rfid == ok_rfid_3 || read_rfid == ok_rfid_4) && (a == 0)) 
  {  
    digitalWrite(lock, LOW);
    digitalWrite(lock2, LOW);
    Serial.println("open relay");
    a++;
    Serial.println(a);
    delay(1000);
    read_rfid = "";
  }
  
  if ((read_rfid == ok_rfid_1 || read_rfid == ok_rfid_2 || read_rfid == ok_rfid_3 || read_rfid == ok_rfid_4) && (a == 1)) 
  {
    Serial.println("cc");
     Keyboard.write('2');
     delay (20);
     Keyboard.write('0');
     delay (20);
     Keyboard.write('2');
     delay (20);
     Keyboard.write('2');
     delay (20);
    Serial.println("close relay");
    a = 0;
    Serial.println(a);
    delay(1000);
  }

  read_rfid = "";
}

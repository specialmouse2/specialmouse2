#include <SPI.h>
#include <Wire.h> 
#include <MFRC522.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
 
#define SS_PIN 10
#define RST_PIN 9
#define LED_G 5 
#define LED_R 4
#define RELAY1 6

MFRC522 mfrc522(SS_PIN, RST_PIN);  

 
void setup() 

{
  lcd.begin();
  lcd.backlight();

  lcd.print("ZENO MODIFF"); 
  lcd.setCursor(0, 1);
  lcd.print("RFID LOCK");
  delay(1000);
  lcd.clear();
  
  SPI.begin();   
  mfrc522.PCD_Init();  
  pinMode(LED_G, OUTPUT);
  pinMode(LED_R, OUTPUT);
  pinMode(RELAY1, OUTPUT);
 

}
void loop() 
{
lcd.clear();
lcd.print("PLACE YOUR");
lcd.setCursor(0, 1);
lcd.print("RFID TAG");
delay(1000);
lcd.clear();




  // Look for new cards
  if ( ! mfrc522.PICC_IsNewCardPresent()) 
  {
    return;
  }
  // Select one of the cards
  if ( ! mfrc522.PICC_ReadCardSerial()) 
  {
    return;
  }
  String content= "";
  byte letter;
  for (byte i = 0; i < mfrc522.uid.size; i++) 
  {
    
     content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
     content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  content.toUpperCase();
  if (content.substring(1) == "2A 17 6E 3C") 
  {
    
   lcd.clear();
   lcd.print("AUTHORIZED ACCESS"); 
   lcd.setCursor(0, 0);
   delay(2000);
   lcd.clear();
   
    digitalWrite(RELAY1, HIGH);
    digitalWrite(LED_G, HIGH);
    delay(6000);
    digitalWrite(RELAY1, LOW);
    digitalWrite(LED_G, LOW);
    delay(1000);
  }
 
 else   {

   lcd.print("UN AUTHORISED"); 
   lcd.setCursor(0, 0);
       digitalWrite(LED_R, HIGH);
    delay(2000);
           digitalWrite(LED_R, LOW);
              lcd.clear();
    delay(2000);

  }
}

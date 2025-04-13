#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SPI.h>
#include <MFRC522.h>
#include <EEPROM.h>
#include "WiFi.h"
#include <HTTPClient.h>

#define SS_PIN 5
#define RST_PIN 4
#define BUZZER_PIN 14
#define NUM_USERS 30  

#define On_Board_LED_PIN  2

MFRC522 rfid(SS_PIN, RST_PIN);
LiquidCrystal_I2C lcd(0x27, 16, 2);

const char* ssid = "iot_board";  //--> Your wifi name
const char* password = "12345678"; //--> Your wifi password
int send_bal;
int team_num;
String inputString = "";


String Web_App_URL = "https://script.google.com/macros/s/AKfycbw-oQwqsn6CURQnir79S_Rc_Q6YC4TImsKHsdaxaR5txzjN8ESHLwwfzIDR47Nk7O85/exec";

String Switch_1_State = "on?";
String Switch_2_State = "off?";

String Status_Read_Sensor = "hello";
float Temp=69;
String Humd="99";

struct User {
    byte uid[4];    
    float balance; 
    int team_number;
};

void saveUsersToEEPROM(User users[]) {
    for (int i = 0; i < NUM_USERS; i++) {
        EEPROM.put(i * sizeof(User), users[i]);
    }
    EEPROM.commit();
}

void loadUsersFromEEPROM(User users[]) {
    for (int i = 0; i < NUM_USERS; i++) {
        EEPROM.get(i * sizeof(User), users[i]);
    }
}

struct User users[NUM_USERS] = {
    {{0x3E, 0x41, 0xEF, 0x57}, 26000.0,1}, 
    {{0x5E, 0xBC, 0x17, 0x58}, 26000.0,2}, 
    {{0x7E, 0x31, 0xC5, 0x56}, 26000.0,3},
    {{0x7E, 0xC5, 0xC9, 0x56}, 26000.0,4},
    {{0x6E, 0x8B, 0xDB, 0x56}, 26000.0,5},
    {{0x8E, 0xE3, 0xE3, 0x57}, 26000.0,6},
    // t1
    {{0x33, 0x7B, 0x4A, 0xFB}, 100000.0,7},
    {{0x53, 0xF4, 0x4C, 0xFB}, 100000.0,8},
    {{0xA, 0xEE, 0x84, 0xE2}, 100000.0,9},
    {{0x33, 0xA1, 0x40, 0xFB}, 100000.0,10},
    {{0xB3, 0x93, 0x4B, 0xFB}, 100000.0,11},
    {{0xCE, 0x29, 0x21, 0x58}, 100000.0,12},
    //t2
    {{0x9E, 0xC7, 0xF1, 0x56}, 100000.0,13},
    {{0x33, 0xAA, 0x46, 0xFB}, 100000.0,14},
    {{0x93, 0x92, 0x45, 0xFB}, 100000.0,15},
    {{0x73, 0xE3, 0x3F, 0xFB}, 100000.0,16},
    {{0x4E, 0xA3, 0xE8, 0x56}, 100000.0,17},
    {{0x3E, 0x7E, 0xF3, 0x57}, 100000.0,18},
    //t3
    {{0x7A, 0xC3, 0x84, 0xE2}, 180000.0,19},
    {{0x23, 0x49, 0x4C, 0xFB}, 180000.0,20},
    {{0xFE, 0x52, 0xED, 0x57}, 180000.0,21},
    {{0x2E, 0xB0, 0xEA, 0x57}, 180000.0,22},
    {{0xAE, 0x24, 0xD8, 0x57}, 180000.0,23},
    {{0x7E, 0xBB, 0xD6, 0x57}, 180000.0,24},
    //t4
    {{0xBE, 0xBA, 0x19, 0x58}, 100000.0,25},
    {{0x93, 0xF7, 0x4C, 0xFB}, 100000.0,26},
    {{0x73, 0xCA, 0x52, 0xFB}, 100000.0,27},
    {{0x23, 0x38, 0x55, 0xFB}, 100000.0,28},
    {{0x2E, 0x3, 0xEB, 0x56}, 100000.0,29},
    {{0x2E, 0xE8, 0xF7, 0x57}, 100000.0,30},
    //t5
};

void setup() {

  // void setup() {
    Serial.begin(115200);
    SPI.begin();
    rfid.PCD_Init();
    lcd.init();
    lcd.backlight();
    pinMode(BUZZER_PIN, OUTPUT);
    pinMode(On_Board_LED_PIN, OUTPUT);

  Serial.println();
  Serial.println("-------------");
  Serial.println("WIFI mode : STA");
  WiFi.mode(WIFI_STA);
  Serial.println("-------------");

  Serial.println();
  Serial.println("------------");
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);


  int connecting_process_timed_out = 20; //--> 20 = 20 seconds.
  connecting_process_timed_out = connecting_process_timed_out * 2;
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    digitalWrite(On_Board_LED_PIN, HIGH);
    delay(250);
    digitalWrite(On_Board_LED_PIN, LOW);
    delay(250);
    if (connecting_process_timed_out > 0) connecting_process_timed_out--;
    if (connecting_process_timed_out == 0) {
      delay(1000);
      ESP.restart();
    }
  }

  digitalWrite(On_Board_LED_PIN, LOW);
  
  Serial.println();
  Serial.println("WiFi connected");
  Serial.println("------------");
  //::::::::::::::::::
  //----------------------------------------
  delay(100);

    EEPROM.begin(NUM_USERS * sizeof(User));

    // Uncomment this to reset EEPROM and store initial values
    //saveUsersToEEPROM(users);

    loadUsersFromEEPROM(users);
}


void loop() 
{
  int transactionAmount = 0;
  int option = 0;
  int mode=0;
  bool isCredit = false;
  while (Serial.available()) 
  {
    inputString = Serial.readStringUntil('\n');  // Read inputString until newline
    int commaIndex = inputString.indexOf(',');  // Find comma position

    if (commaIndex == -1) 
    {
      Serial.println("Invalid Input! Format: CR,amount or DB,amount");
      return;
    }    

    String transactionType = inputString.substring(0, commaIndex);  // Extract transaction type
    float amount = inputString.substring(commaIndex + 1).toFloat();  // Extract amount and convert to float

    if (transactionType == "CR")
    {
      option=2;
      //isCredit=2;
      transactionAmount=amount;
    } 
    else if (transactionType == "DB") 
    {
      option=1;
      //isCredit=1;
      transactionAmount= amount;
    } 
    else if (transactionType=="Bal")
    {
      option=3;
    }
    else 
    {
      Serial.println("Invalid Transaction Type! Use CR or DB.");
      return;
    }
  
    

    if(isCredit = (option == 1))
    {
      lcd.clear();
      lcd.print("Debit Amount: ");
      lcd.setCursor(0, 1);
      lcd.print(transactionAmount);
    }
    if(isCredit = (option == 2))
    {
      lcd.clear();
      lcd.print("Credit Amount: ");
      lcd.setCursor(0, 1);
      lcd.print(transactionAmount);
    }


    delay(3000);

    lcd.clear();
    lcd.print("Scan Your Card");
    Serial.println("Place an RFID tag near the reader...");

    while (!rfid.PICC_IsNewCardPresent() || !rfid.PICC_ReadCardSerial()) {}

    byte scannedUID[4];
    for (byte i = 0; i < 4; i++) {
        scannedUID[i] = rfid.uid.uidByte[i];
    }

    digitalWrite(BUZZER_PIN, HIGH);
    delay(200);
    digitalWrite(BUZZER_PIN, LOW);
    int userIndex = -1;
    for (int i = 0; i < NUM_USERS; i++) {
        if (memcmp(scannedUID, users[i].uid, 4) == 0) {
            userIndex = i;
            break;
        }
    }

    if (userIndex != -1) {
        if (option == 3) {
            lcd.clear();
            lcd.print("Team Number : ");
            lcd.print(users[userIndex].team_number);
            team_num=users[userIndex].team_number;
            delay(3000);
            lcd.clear();
            lcd.setCursor(0, 0);
            lcd.print("Balance: Rs.");
            lcd.setCursor(0, 1);
            lcd.print(users[userIndex].balance);
            Serial.print("Current Balance: Rs.");
            Serial.println(users[userIndex].balance);
            send_bal=users[userIndex].balance;
            Serial.println(send_bal);
            delay(3000);
        } else {
            float previousBalance = users[userIndex].balance;

            if (isCredit) {
                users[userIndex].balance += transactionAmount;
            } else {
                if (users[userIndex].balance >= transactionAmount) {
                    users[userIndex].balance -= transactionAmount;
                } else {
                    lcd.clear();
                    lcd.print("Team Number : ");
                    lcd.print(users[userIndex].team_number);
                    delay(3000);
                    lcd.clear();
                    lcd.print("Insufficient");
                    lcd.setCursor(0,1);
                    lcd.print("           Funds");
                    Serial.println("Transaction Failed: Insufficient Balance!");
                    delay(3000);
                    return;
                }
            }

            EEPROM.put(userIndex * sizeof(User), users[userIndex]);
            EEPROM.commit();
            lcd.clear();
            lcd.print("Team Number : ");
            lcd.print(users[userIndex].team_number);
            team_num=users[userIndex].team_number;
            delay(3000);
            lcd.clear();
            lcd.setCursor(0, 0);
            lcd.print("Prev: Rs.");
            lcd.print(previousBalance);
            lcd.setCursor(0, 1);
            lcd.print("New: Rs.");
            lcd.print(users[userIndex].balance);

            Serial.print("Previous Balance: Rs.");
            Serial.println(previousBalance);
            Serial.print("New Balance: Rs.");
            Serial.println(users[userIndex].balance);
            send_bal=users[userIndex].balance;
            Serial.println(send_bal);
            delay(2000);
        }
    } else {
        Serial.print("Unauthorized Card! UID: ");
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("Unauthorized!");
        lcd.setCursor(0,1);
        lcd.print("            Card");

        lcd.setCursor(0, 1);
        for (byte i = 0; i < 4; i++) {
            Serial.print(scannedUID[i], HEX);
            Serial.print(" ");
            lcd.print(scannedUID[i], HEX);
            lcd.print(" ");
        }
        Serial.println();

        delay(3000);

    }

    rfid.PICC_HaltA();
    rfid.PCD_StopCrypto1();
    if (WiFi.status() == WL_CONNECTED) {
      digitalWrite(On_Board_LED_PIN, HIGH);

      // Create a URL for sending or writing inputString to Google Sheets.
      String Send_inputString_URL = Web_App_URL + "?sts=write";
      Send_inputString_URL += "&srs=" + String(send_bal);
      Send_inputString_URL += "&temp=" + String(team_num);

      Serial.println();
      Serial.println("-------------");
      Serial.println("Send inputString to Google Spreadsheet...");
      Serial.print("URL : ");
      Serial.println(Send_inputString_URL);

      //::::::::::::::::::The process of sending or writing inputString to Google Sheets.
        // Initialize HTTPClient as "http".
        HTTPClient http;
    
        // HTTP GET Request.
        http.begin(Send_inputString_URL.c_str());
        http.setFollowRedirects(HTTPC_STRICT_FOLLOW_REDIRECTS);
    
        // Gets the HTTP status code.
        int httpCode = http.GET(); 
        Serial.print("HTTP Status Code : ");
        Serial.println(httpCode);
    
        // Getting response from google sheets.
        String payload;
        if (httpCode > 0) {
          payload = http.getString();
          Serial.println("Payload : " + payload);    
        }
                    
        http.end();
      //::::::::::::::::::
      
      digitalWrite(On_Board_LED_PIN, LOW);
      digitalWrite(BUZZER_PIN,HIGH);
      delay(300);
      digitalWrite(BUZZER_PIN, LOW);
      delay(300);
      digitalWrite(BUZZER_PIN,HIGH);
      delay(300);
      digitalWrite(BUZZER_PIN, LOW);
      Serial.println("-------------");
    //----------------------------------------
  }
  lcd.clear();
  delay(500);
  lcd.print("Bank of Aspera");
  lcd.setCursor(0,1);
  lcd.print("enter command");
}

}

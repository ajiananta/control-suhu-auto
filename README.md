#include <SoftwareSerial.h>

#include <DHT.h>
#define DHTTYPE DHT22   // DHT 22  (AM2302)
#include <LiquidCrystal.h>
#include <EEPROM.h>
const int  DHTPIN =12;     // what pin we're connected to

float temp; 
const int fan = A1;
const int heater = A2;
const int rs = 7, en = 6, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
int Option,Up,Down,Ok;
int POption=0,PUp=0,PDown=0,POk=0;
int Menu,sub=0,mode=0;
int tambah;
int kurang;
int h;
int s = 1;
int n = 2;
int a = 3;
char val;
int tunggu = 0;
int start_on;
int dly_start_on = 20;
//int maxHum = 200;
//int maxTemp = 45;
int romsuhu = 1;
int romkelembapan = 2;
int romhot = 4;
int MenPilihan = 3;
LiquidCrystal lcd(7, 6, 5, 4, 3, 2);
unsigned long interval=1000; // the time we need to wait
unsigned long pMs=0; // millis() returns an unsigned long.
DHT dht(DHTPIN, DHTTYPE);


void setup()
{
  pinMode(11, INPUT_PULLUP);// mengaktifkan resistor internal
  pinMode(10, INPUT_PULLUP);
  pinMode(9, INPUT_PULLUP);
  pinMode(8, INPUT_PULLUP);
  pinMode(A1, OUTPUT);
   pinMode(A2, OUTPUT);
  digitalWrite(fan, HIGH);
  digitalWrite(heater, HIGH);
  lcd.begin(16, 2);
  //Serial.begin(9600);
 Serial.begin(9600); //Sesuaikan dengan Baudrate Modul Bluetooth anda!
if( Serial.available() >0 ) {
 val = Serial.read();}
  
}

void loop() 
{
int suhu = EEPROM.read(romsuhu);
int kelembapan = EEPROM.read(romkelembapan);
int Pilihan = EEPROM.read(MenPilihan); 
int bpanas = EEPROM.read(romhot); 
  Option= digitalRead(10);
  Up=   digitalRead(11);
  Down= digitalRead(9);
  Ok=   digitalRead(8);
 tampil();
 
 val = Serial.read();
 //Serial.print(suhu);

//------------------------------ back Option utama tombol
  //if (val=='1'){Option==0 && POption==0;}
  //if (Option==0 && POption==0){POption=1;}
  if (val=='1' || Option==0 && POption==0){POption=1;}
  if (Option!=0 && POption==1 && Menu==0){lcd.clear(); POption=0; Menu=9; }
  if (Option!=0 && POption==1 && Menu==9){lcd.clear(); POption=0; Menu=0; }
  if (Option!=0 && POption==1 && Menu==8){lcd.clear(); POption=0; Menu=9; }
  if (Option!=0 && POption==1 && Menu==1){lcd.clear(); POption=0; Menu=9; }
  if (Option!=0 && POption==1 && Menu==5){lcd.clear(); POption=0; Menu=8; }
//------------------------------------- remot

  
}

void tampil() {
if (Menu==0) {
  val = Serial.read();
  
int suhu = EEPROM.read(romsuhu);
int kelembapan = EEPROM.read(romkelembapan);
int Pilihan = EEPROM.read(MenPilihan); 
int bpanas = EEPROM.read(romhot); 
  float t = dht.readTemperature();// Reading temperature or humidity takes about 250 milliseconds!
                                  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  float h = dht.readHumidity(); // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t)) {
    lcd.setCursor(0,0); lcd.print("Failed to read from DHT sensor!");
    return;
  }
  lcd.setCursor(16,1); lcd.print(Pilihan);
  if (Pilihan == 1 )
  {
   if(t > suhu) {
      if (tunggu < 1000) {
      tunggu ++;  

      }
    else{
      tunggu = 0;
      digitalWrite(fan, LOW);
      lcd.setCursor(10,0); lcd.print("Col ON"); 
      }
     
      }
      else {
        tunggu=0;
     digitalWrite(fan, HIGH);
     lcd.setCursor(10,0); lcd.print("Col OF"); }}
  if (Pilihan == 2 )
  {

   if( t < suhu) {
      if (tunggu < 1000) {
      tunggu ++;  

      }
    else{
      tunggu = 0;
      digitalWrite(fan, LOW);
      lcd.setCursor(10,0); lcd.print("Hot ON"); 
      }
     
      }
      else {
        tunggu=0;
     digitalWrite(fan, HIGH);
     lcd.setCursor(10,0); lcd.print("Hot OF"); }}

 if (Pilihan == 3 ) 
      {
        if( t < suhu) {
     digitalWrite(fan, HIGH);
     lcd.setCursor(10,0); lcd.print("Col OF"); 
              if(t < bpanas) {
               digitalWrite(heater, LOW);
               lcd.setCursor(10,0); lcd.print("Hot ON"); }
                                else {      
                            digitalWrite(heater, HIGH);
                            // lcd.setCursor(10,0); lcd.print("Hot OF"); 
                            }}
     
     
    else{
      tunggu = 0;
      digitalWrite(fan, LOW);
      lcd.setCursor(10,0); lcd.print("Col ON"); 
      } }

     

  lcd.setCursor(0,0); lcd.print("Tm:"); 
  lcd.setCursor(3,0); lcd.print(t);
lcd.setCursor(8,0); lcd.print("c");
 lcd.setCursor(0,1); lcd.print("Hm:"); 
  lcd.setCursor(3,1); lcd.print(h);
  lcd.setCursor(8,1); lcd.print("%");
  lcd.setCursor(13,1); lcd.print(suhu);
  lcd.setCursor(15,1); lcd.print("c");

 
    }

 if (Menu==9){
        
      lcd.setCursor(0,0); lcd.print("Menu :");
 val = Serial.read();
      if (Down==0 && PDown==0){PDown=1;}  //turunkan Cursor ">"
      if (Down!=0 && PDown==1){lcd.clear(); PDown=0; mode++;} 
      
     
      if (Up==0 && PUp==0){PUp=1;}   //naikkan Cursor ">"
      if (Up!=0 && PUp==1){lcd.clear(); PUp=0; mode--;}
       switch (mode)
      {
     
        case 0 : lcd.setCursor(6,0); lcd.print("Pendingin"); break;
        case 1 : lcd.setCursor(6,0); lcd.print("Pemanas"); break;
        case 2 : lcd.setCursor(6,0); lcd.print("Auto"); break;
      }  
 
      if (Ok==0 && POk==0){POk=1;}     
      if (Ok!=0 && POk==1 && mode==0){EEPROM.update(MenPilihan, s);POk=0; Menu=1; lcd.clear(); } // pilih Option 1 
       if (Ok!=0 && POk==1 && mode==1){EEPROM.update(MenPilihan, n);POk=0; Menu=1;  lcd.clear(); } // pilih Option 2
       if (Ok!=0 && POk==1 && mode==2){EEPROM.update(MenPilihan, a);POk=0; Menu=8;  lcd.clear(); }

           
       if (mode>2 || mode<0) mode=0;
        
        }


if (Menu==8)
  {
      // tampilan seting//
      val = Serial.read();
      lcd.setCursor(6,0); lcd.print("Batas COL :"); 
      lcd.setCursor(6,1); lcd.print("Batas HOT :"); 
       
     
      if (Down==0 && PDown==0){PDown=1;}  //turunkan Cursor ">"
      if (Down!=0 && PDown==1){lcd.clear(); PDown=0; mode++;} 
      
      if (Up==0 && PUp==0){PUp=1;}   //naikkan Cursor ">"
      if (Up!=0 && PUp==1){lcd.clear(); PUp=0; mode--;}
      

       switch (mode)
      {
     
        case 0 : lcd.setCursor(4,0); lcd.print("->"); break;
 
        case 1 : lcd.setCursor(4,1); lcd.print("->"); break;
      }  


      if (Ok==0 && POk==0){POk=1;}
      if (Ok!=0 && POk==1 && mode==0){POk=0; Menu=6; lcd.clear(); } // pilih Option 1       
      if (Ok!=0 && POk==1 && mode==1){POk=0; Menu=5;  lcd.clear(); } // pilih Option 2 
      if (mode>1 || mode<0) mode=0;

   
  }


  
  if (Menu==1)
  {
      // tampilan seting//
      val = Serial.read();
      lcd.setCursor(0,0); lcd.print("Set");
      lcd.setCursor(6,0); lcd.print("Temperatur"); 
      lcd.setCursor(6,1); lcd.print("Humidity"); 
       
    
      if (Down==0 && PDown==0){PDown=1;}  //turunkan Cursor ">"
      if (Down!=0 && PDown==1){lcd.clear(); PDown=0; mode++;} 
     
      if (Up==0 && PUp==0){PUp=1;}   //naikkan Cursor ">"
      if (Up!=0 && PUp==1){lcd.clear(); PUp=0; mode--;}


       switch (mode)
      {
     
        case 0 : lcd.setCursor(4,0); lcd.print("->"); break;
 
        case 1 : lcd.setCursor(4,1); lcd.print("->"); break;
      }  


   
      if (Ok==0 && POk==0){POk=1;}
      if (Ok!=0 && POk==1 && mode==0){POk=0; Menu=6; lcd.clear(); } // pilih Option 1          
      if (Ok!=0 && POk==1 && mode==1){POk=0; Menu=7;  lcd.clear(); } // pilih Option 2 
      if (Ok!=0 && POk==1 && mode==2){POk=0; Menu=8;  lcd.clear(); } // pilih Option 2 
       
       if (mode>1 || mode<0) mode=0;

   
  }
//-------------------------- isi setup Option 1-------------------------

if (Menu==5){
   val = Serial.read();
  // suhu = temperatur
 int bpanas = EEPROM.read(romhot); 
  
  lcd.setCursor(0,0); lcd.print("Temperatur : ");
  lcd.setCursor(14,0); lcd.print(bpanas); 
  lcd.setCursor(0,1); lcd.print("->");
  
  lcd.setCursor(14,1); lcd.print(tambah); 
  //----------------- remot ------------
  
  //------------------ manual-----------------

   if (Up==0 && PUp==0){ 
         lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Temperatur: ");
   lcd.setCursor(14,0);
  lcd.print(bpanas); 
  tambah++; 
   lcd.setCursor(14,1); 
   lcd.print(tambah); 
  PUp==1;
  Up = 1;
  delay(150); 
  }


  if (Down==0 && PDown==0){ //turunkan Cursor ">"
     lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Temperatur: ");
   lcd.setCursor(14,0);
  lcd.print(bpanas); 
  tambah--;
   lcd.setCursor(14,1);
  lcd.print(tambah); 
  PDown==1;
  Down ==1;
  delay(150); 
  }
  bpanas=constrain(bpanas, 0, 80);
  //-------------------------------- ok rmot ----------------------

  //---------------------------------- ok manual-----------------
  
 if (Ok==0 && POk==0){
 EEPROM.update(romhot, tambah);
 lcd.clear();
 lcd.print(" Sukses Save ");
 
  delay(300);
   
  lcd.clear();
  Menu=8;
 } 

// if (val=='1'){Option==0 && POption==0;}
 // if (Option==0 && POption==0){
 //lcd.clear();
 //lcd.print(" Back ");
  //delay(300);
   //lcd.clear();
 // Menu=8;
  //}
  }


if (Menu==6){
  // suhu = temperatur
  int suhu = EEPROM.read(romsuhu);
  int pilihan = EEPROM.read(MenPilihan);
  lcd.setCursor(0,0); lcd.print("Temperatur : ");
  lcd.setCursor(14,0); lcd.print(suhu); 
  lcd.setCursor(0,1); lcd.print("->");
  
  lcd.setCursor(14,1); lcd.print(tambah); 
  //----------------- remot ------------
  
  //------------------ manual-----------------
  
   if (Up==0 && PUp==0){ 
         lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Temperatur: ");
   lcd.setCursor(14,0);
  lcd.print(suhu); 
  tambah++; 
   lcd.setCursor(14,1); 
   lcd.print(tambah); 
  PUp==1;
  Up = 1;
  delay(150); 
  }

   
  if (Down==0 && PDown==0){ //turunkan Cursor ">"
     lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Temperatur: ");
   lcd.setCursor(14,0);
  lcd.print(suhu); 
  tambah--;
   lcd.setCursor(14,1);
  lcd.print(tambah); 
  PDown==1;
  Down ==1;
  delay(150); 
  }
  suhu=constrain(suhu, 0, 80);
  //-------------------------------- ok rmot ----------------------

  //---------------------------------- ok manual-----------------

 if (Ok==0 && POk==0){
 EEPROM.update(romsuhu, tambah);
 lcd.clear();
 lcd.print(" Sukses Save ");
  delay(300);
  if (pilihan ==3){
  lcd.clear();
  Menu=8;}
  else{Menu=1;}
 } 

 
  if (Option==0 && POption==0){
 lcd.clear();
 lcd.print(" Back ");
  delay(300);
  if (pilihan ==3){
  lcd.clear();
  Menu=8;}
  else{Menu=1;}
  }
  }


    // ----------------------------set kelembapan Option 1.2 ------------------------------------------------------
  if (Menu==7){
    val = Serial.read();
    //kelembapan = humidity
  int kelembapan = EEPROM.read(romkelembapan);
  
  lcd.setCursor(0,0);
  lcd.print("Humidity: ");
  lcd.setCursor(13,0);
  lcd.print(kelembapan); 
  lcd.setCursor(0,1);
  lcd.print("->"); 
   //lcd.setCursor(13,1);
 // lcd.print(kelembapan);

   if (Up==0 && PUp==0){   //naikkan Cursor ">"
         lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Humidity: ");
   lcd.setCursor(13,0);
  lcd.print(kelembapan);
  kelembapan++;
  EEPROM.update(romkelembapan, kelembapan);//data yang terdapat dalam EEPROM (blok 1) akan ditambah 1
  lcd.setCursor(13,0);
  lcd.print(kelembapan); 
  delay(150); 
  }

  if (Down==0 && PDown==0){ //turunkan Cursor ">"
     lcd.clear();
     lcd.setCursor(0,0);
  lcd.print("Humidity: ");
   lcd.setCursor(13,0);
  lcd.print(kelembapan); 
  kelembapan--; //data yang terdapat dalam EEPROM (blok 1) akan ditambah 1
  EEPROM.update(romkelembapan, kelembapan);
  lcd.setCursor(13,0);
  lcd.print(kelembapan); 
  delay(150); 
  }
kelembapan=constrain(kelembapan, 0, 100);


 if (Ok==0 && POk==0){
 lcd.clear();
 lcd.print(" Sukses Save ");
 
  delay(300);
   
  lcd.clear();
  Menu=1;
 }


 if (Option==0 && POption==0){
 lcd.clear();
 lcd.print(" Back ");
  delay(300);
   lcd.clear();
  Menu=1;
  }}
  
// ---------------------------- -------------------------------
}

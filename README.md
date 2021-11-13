//Code Gabung
//levelV 3 led 2 WEBHOOK NOTI VALUE DISPLAY
//BLYNK

//water level sensor 3
//raindrop sensor 1
//waterflow sensor 1
//ESP32

#define BLYNK_PRINT Serial
#include <WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp32.h>

char auth[] = "FUXGSpxyEHJYQ0kgU1nVJSgZh1_0m1aT";
char ssid[] = "Syahmi Rohaizad";
char pass[] = "mcronys123";

#define sensorPower 21
#define powerPin 32
#define waterLevel_1 34
#define waterLevel_2 33
#define waterLevel_3 39

#define rainSensePin 35

volatile int flow_frequency;
float vol = 0.0, l_minute;
unsigned char flowsensor = 27;
unsigned long currentTime;
unsigned long cloopTime;
int curCounter = 0;
int val1 = 0;
int val2 = 0;
int val3 = 0;


int level1 = 0;
int level2 = 0;
int level3 = 0;

int rainSenseReading;



void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Blynk.begin(auth, ssid, pass);
  pinMode(waterLevel_1, INPUT);
  pinMode(waterLevel_2, INPUT);
  pinMode(waterLevel_3, INPUT);

  pinMode(rainSensePin, INPUT);
  pinMode(flowsensor, INPUT);
  pinMode(sensorPower, OUTPUT);
  pinMode(powerPin, OUTPUT);
  digitalWrite(powerPin, HIGH);
  digitalWrite(flowsensor, HIGH);
  digitalWrite(sensorPower, LOW);


  digitalWrite(sensorPower, HIGH);

  attachInterrupt(digitalPinToInterrupt(flowsensor), flow, RISING);
  currentTime = millis();
  cloopTime = currentTime;

}

void loop() {


  Blynk.run();
  level1 = readSensor1();
  level2 = readSensor2();
  level3 = readSensor3();

  rainSenseReading = analogRead(rainSensePin);


  // put your main code here, to run repeatedly:
  Serial.print("Water level 1: ");
  Serial.print(readSensor1());
  Serial.print("    ");
  Serial.print("2: ");
  Serial.print(readSensor2());
  Serial.print("    ");
  Serial.print("3: ");
  Serial.print(readSensor3());
  Serial.print("    ");
  Serial.print("4: ");
  Serial.print(readSensor1());
  Serial.print("    ");
  Serial.print("5: ");
  Serial.print(readSensor2());
  Serial.print("    ");
  Serial.print("6: ");
  Serial.println(readSensor3());

  Serial.print("Tahap Hujan: ");
  Serial.println(rainSenseReading);
  
if(rainSenseReading<3000){
  Blynk.virtualWrite(V4, 255); //green
   Blynk.virtualWrite(V5, 0); //red
}
else{
  Blynk.virtualWrite(V4, 0); //green
   Blynk.virtualWrite(V5, 255); //red
  }
  Serial.print("Supporting sensor");


  delay(1000);

   currentTime = millis();
  if (currentTime >= (cloopTime + 1000))
  {
    cloopTime = currentTime;
    if (flow_frequency != 0)
    {
      l_minute = (flow_frequency / 7.5);
      flow_frequency = 0;
      Serial.print(l_minute, DEC);
      Serial.println(" L/Sec");
      Blynk.virtualWrite(V6, l_minute);
    }
    else
    {
      Serial.println(" flow rate = 0 ");
    }
  }
}



void flow ()
{
  flow_frequency++;
}

int readSensor1()
{
  delay(10);
  val1 = analogRead(waterLevel_1);
  Blynk.virtualWrite(V1, level1);
  return val1;
}

int readSensor2()
{

  delay(10);
  val2 = analogRead(waterLevel_2);
  Blynk.virtualWrite(V2, level2);
  return val2;
}

int readSensor3()
{

  delay(10);
  val3 = analogRead(waterLevel_3);
  Blynk.virtualWrite(V3, level3);
  return val3;
}

# Toll-Gate

#include <HCSR04.h>
#include <Servo.h>

// Ultrasonic sensor configuration
const uint8_t usTrigPin = 2;
const uint8_t usEchoPin = 3;
HCSR04 usSensor(usTrigPin, usEchoPin);

// Servo motor configuration
const uint8_t servoPin = 6;
Servo motor;

// Operational logic variables
const uint16_t triggerDistance = 15;      //distance in cm to detect vehicle
const uint16_t gateOpenDuration = 5;      //time in seconds to keep the gate open
float measuredDistance = 0.0;             //distance measured by the ultrasonic sensor
uint32_t lastGateOpenTime = 0;            //last time when gate was opened
bool isGateOpen = false;                  //current state of gate (open or closed)

// Function Prototypes
bool carAtGate();
void openGate();
void closeGate();

void setup(){
  //Serial.begin(9600);
  //Serial.println("Welcome to Toll Management System");
  
  motor.attach(servoPin);
  motor.write(0);     //initialize motor position to 90 angle
  delay(3000);
}

void loop(){
  if(carAtGate()){
    openGate();
  }

  closeGate();
  delay(500);
}

bool carAtGate(){
  measuredDistance = usSensor.dist();
  //This is done to round up the distance to the nearest integer
  int distance = (int)(((measuredDistance * 100) + 50) / 100);
  //Serial.println(distance);

  return (distance <= triggerDistance);
}

void openGate(){
  if(!isGateOpen){
    motor.write(90);
    lastGateOpenTime = millis();
    isGateOpen = true;
    //Serial.println("Gate Opened");    
  }
}

void closeGate(){
  if(isGateOpen){
    if((millis() - lastGateOpenTime) > (gateOpenDuration * 1000)){
      motor.write(0);
      lastGateOpenTime = 0;
      isGateOpen = false;
      //Serial.println("Gate Closed");
    }  
  }
}


https://github.com/user-attachments/assets/5df4df1b-6671-4095-9f1c-8885793dc55a


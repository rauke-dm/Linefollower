
[FINALE CODE - RDM.pdf](https://github.com/rauke-dm/Linefollower/files/12489485/FINALE.CODE.-.RDM.pdf)


#include "SerialCommand.h"
#include "EEPROMAnything.h"

#define SerialPort Serial1
#define Baudrate 9600

#define rotationL 5 //5
#define rotationR 10 //10
#define speedM1 6 //6
#define speedM2 9 //9

const byte StartStop = 3;

float iTerm = 0;
float lastErr = 0;

SerialCommand sCmd(SerialPort);
bool debug;
unsigned long previous, calculationTime;
bool run = false;

const int sensor[] = {A0, A1, A2, A3, A4, A5};
struct param_t
{
  unsigned long cycleTime;
  int black[6];
  int white[6];
  int power;
  float diff;
  float kp;
  float ki;
  float kd;
 } params;

void setup()
{
  Serial1.begin(Baudrate);

  sCmd.addCommand("set", onSet);
  sCmd.addCommand("calibrate", onCalibrate);
  sCmd.addCommand("debug", onDebug);

  sCmd.setDefaultHandler(onUnknownCommand);

  EEPROM_readAnything(0, params);

  pinMode(13, OUTPUT);
  Serial1.println("ready");

  //pinMode(StartStop, INPUT_PULLUP);
 // attachInterrupt(digitalPinToInterrupt(StartStop), ProgrammaRun , FALLING);
}

void loop()
{
  sCmd.readSerial();

  unsigned long current = micros();
  if (current - previous >= params.cycleTime)
  {
    previous = current;

//    SerialPort.println ("loop");
    
    int normalised[6];

    for (int i = 0; i < 6; i++)//6
    {
      normalised[i] = map(analogRead(sensor[i]), params.black[i], params.white[i], 0, 1000);


    Serial1.print(i);
    Serial1.print(": ");  
    Serial1.print(normalised[i]);
    
    Serial1.print(" ");
    
     
    
    }
    Serial1.println();

    /* interpolatie */
    int index = 0;
    float position;
    for (int i = 0; i < 6; i++) if (normalised[i] < normalised[index]) index = i;   //index = zwartste sensor

    if (normalised[index] > 700) run = false;

      if (index == 0) position = -30; //0
      else if (index == 5) position = 30; //5
    else
    {
      int sNul = normalised[index];
      int sMinEen = normalised [index - 1];
      int sPlusEen = normalised[index + 1];

      float b = sPlusEen - sMinEen;
      b = b / 2;

      float a = sPlusEen - b - sNul;

      position = -b / (2 * a); 
      position += index;
      position -= 2.5;

      position *= 9.525;

      
    }
    Serial1.print("Positie: ");
    Serial1.println(position);

    /* Proportionele regelaar */
    
    float error = -position;
    float output = error * params.kp;
 //   SerialPort.println(output);

    /* Integrerend */

    iTerm += params.ki * error;
    iTerm = constrain(iTerm, -510, 510);
    output += iTerm;

    /* Differentiërend */

    output += params.kd * (error - lastErr);
    lastErr = error;


    output = constrain(output, -params.power, params.power); //-510, 510
    Serial1.println(output);
    int powerLeft = 0;
    int powerRight = 0;
    
    if (run)
    {
 //     SerialPort.println("RUN");
      

      if (output >= 0) //>
      {
        powerLeft = constrain(params.power + params.diff * output, -30, 30); // -255, 255
        powerRight = constrain(powerLeft - output, -30, 30); // -255, 255
        powerLeft = powerRight + output;

        
        
      }
      else

      {
        powerRight = constrain(params.power - params.diff * output, -30, 30); // -255, 255
        powerLeft = constrain(powerRight + output, -30, 30); // -255, 255
        powerRight = powerLeft - output;

       
        
      }

    //  if (powerLeft >=0) {
  
  //     analogWrite(speedM1, abs(powerLeft)); //abs
    //  }
      
   //   else {
   //    analogWrite(rotationL, abs(powerLeft));  
    //  }

   //   if (powerRight >=0) {
  
   //    analogWrite(speedM2, abs(powerRight)); //abs
   //   }
      
   //   else {
  //     analogWrite(rotationR, abs(powerRight));  
  //    }



      

      //if (powerLeft < 0) digitalWrite(rotationR, LOW);
     // if (powerLeft > 0) digitalWrite(rotationR, HIGH);
     //if (powerRight < 0) digitalWrite(rotationL, LOW);
     //if (powerRight > 0) digitalWrite(rotationL, HIGH);

     //digitalWrite(rotationR, LOW);
     //digitalWrite(rotationL, LOW);

      analogWrite(speedM1, abs(powerLeft)); //abs
      analogWrite(speedM2, abs(powerRight));
      
      Serial1.print("powerleft: ");
      Serial1.print(powerLeft);
      Serial1.print(" powerright: ");
      Serial1.print(powerRight);   
      Serial1.println(" ");   
      
//      SerialPort.println("speedM1: ");
 //     SerialPort.print(speedM1);
  //    SerialPort.print(" speedM2: ");
  //    SerialPort.print(speedM2);
    }
   else  // run = false;
    {

      digitalWrite(rotationR, LOW);
      digitalWrite(rotationL, LOW);

      analogWrite(speedM1, 0);
      analogWrite(speedM2, 0);
    }

  }

  unsigned long difference = micros() - current;
  if (difference > calculationTime) calculationTime = difference;
}

void onUnknownCommand(char *command)
{
  Serial1.print("unknown command: \"");
  Serial1.print(command);
  Serial1.println("\"");
}

void ProgrammaRun() {
  run = ! run;
  iTerm = 0;
  lastErr = 0;
}

void onSet()
{
  char* param = sCmd.next();
  char* value = sCmd.next();

  if (strcmp(param, "cycle") == 0)
  {
    params.cycleTime = atol(value);
    long newCycleTime = atol(value);
    float ratio = ((float) newCycleTime) / ((float) params.cycleTime);

    params.ki *= ratio;
    params.kd /= ratio;

    params.cycleTime = newCycleTime;

  }

  else if (strcmp(param, "ki") == 0)
  {
    float cycleTimeInSec = ((float) params.cycleTime) / 1000000;
    params.ki = atof(value) * cycleTimeInSec;
  }

  else if (strcmp(param, "kd") == 0)
  {
    float cycleTimeInSec = ((float) params.cycleTime) / 1000000;
    params.kd = atof(value) / cycleTimeInSec;
  }
  else if (strcmp(param, "run") == 0) run = ! run; //;atol(value)
  else if (strcmp(param, "power") == 0) params.power = atol(value);
  else if (strcmp(param, "diff") == 0) params.diff = atof(value);
  else if (strcmp(param, "kp") == 0) params.kp = atof(value);

  EEPROM_writeAnything(0, params);
}

void onDebug()
{
  Serial1.print("cycle time: ");
  Serial1.println(params.cycleTime);

  Serial1.print("black: ");
  for (int i = 0; i < 6; i++) {
    Serial1.print(params.black[i]);
    Serial1.print(" ");
  }
  Serial1.println(" ");

  Serial1.print("white: ");
  for (int i = 0; i < 6; i++) {
    Serial1.print(params.white[i]);
    Serial1.print(" ");
  }
  Serial1.println(" ");

  Serial1.print("kp: ");
  Serial1.println(params.kp);

  float cycleTimeInSec = ((float) params.cycleTime) / 1000000;
  float ki = params.ki / cycleTimeInSec;
  Serial1.print("Ki: ");
  Serial1.println(ki);

  float kd = params.kd * cycleTimeInSec;
  Serial1.print("Kd: ");
  Serial1.println(kd);

  Serial1.print("diff: ");
  Serial1.println(params.diff);

  Serial1.print("power: ");
  Serial1.println(params.power);

  Serial1.print("calculation time: ");
  Serial1.println(calculationTime);
  calculationTime = 0;
}


void onCalibrate()
{
  char* param = sCmd.next();

  if (strcmp(param, "black") == 0)
  {
    Serial1.print("start calibrating black... ");
    for (int i = 0; i < 6; i++) params.black[i] = analogRead(sensor[i]);
    Serial1.println("done");
  }
  else if (strcmp(param, "white") == 0)
  {
    Serial1.print("start calibrating white... ");
    for (int i = 0; i < 6; i++) params.white[i] = analogRead(sensor[i]);
    Serial1.println("done");
  }

  EEPROM_writeAnything(0, params);
}

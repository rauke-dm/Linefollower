# Sensoren proof of concept

minimale hard- en software die aantoont dat minimaal 6 sensoren onafhankelijk van elkaar kunnen uitgelezen worden en dat daarbij een zo groot mogelijk bereik van de AD converter benut wordt (indien van toepassing)

Elektronisch schema: [POC QTR-8A.pdf](https://github.com/rauke-dm/Linefollower/files/12489331/POC.QTR-8A.pdf)

Minimale Code: 
#include "SerialCommand.h"
#include "EEPROMAnything.h"

#define SerialPort Serial
#define Baudrate 9600

const byte StartStop = 3;

float iTerm = 0;
float lastErr = 0;

SerialCommand sCmd(SerialPort);
bool debug;
unsigned long previous, calculationTime;
bool run = true;

const int sensor[] = {A0, A1, A2, A3, A4, A5};
struct param_t
{
  unsigned long cycleTime;
  int black[6];
  int white[6];

 } params;

void setup()
{
  SerialPort.begin(Baudrate);

  EEPROM_readAnything(0, params);

}

void loop()
{
  sCmd.readSerial();
delay (1000);
  unsigned long current = micros();
  if (current - previous >= params.cycleTime)
  {
    previous = current;

//    SerialPort.println ("loop");
    
    int normalised[6];
    for (int i = 1; i < 6; i++)
    {
      normalised[i] = map(analogRead(sensor[i]), params.black[i], params.white[i], 0, 1000);  
    SerialPort.print(normalised[i]);
    SerialPort.print(" ");
    }
  }    
  SerialPort.println(); }

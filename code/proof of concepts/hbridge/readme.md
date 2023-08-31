# H-Bridge proof of concept

Minimale hard- & software die aantoont dat 2 motoren onafhankelijk van elkaar kunnen draaien, en regelbaar zijn in snelheid en draairichting.

Elektronisch schema: [POC DRV8835.pdf](https://github.com/rauke-dm/Linefollower/files/12489307/POC.DRV8835.pdf)

Minimale code:
#define RotatieLinks 5

#define RotatieRechts 10

#define SnelheidMotor1 6

#define SnelheidMotor2 9

void setup()  {
  
}

void loop() {
  
 analogWrite(SnelheidMotor1, 20);
 
 delay(2000);
analogWrite(SnelheidMotor1, 40);
 delay(2000);
 analogWrite(SnelheidMotor1, 60);
 delay(2000);
 analogWrite(SnelheidMotor1, 0);
 delay(2000);

   
 analogWrite(SnelheidMotor2, 20);
 delay(2000);
 analogWrite(SnelheidMotor2, 40);
 delay(2000);
 analogWrite(SnelheidMotor2, 60);
 delay(2000);    
 analogWrite(SnelheidMotor2, 0);
 delay(2000);

  analogWrite(RotatieLinks, 20);
 delay(2000);
 analogWrite(RotatieLinks, 40);
 delay(2000);
 analogWrite(RotatieLinks, 60);
 delay(2000);
 analogWrite(RotatieLinks, 0);
 delay(2000);
   
 analogWrite(RotatieRechts, 20);
 delay(2000);
 analogWrite(RotatieRechts, 40);
 delay(2000);
 analogWrite(RotatieRechts, 60);
 delay(2000);   
 analogWrite(RotatieRechts, 0);
 delay(2000);
}
 


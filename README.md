# sample-
#include<EEPROM.h>
#include <Servo.h> 
 Servo myservo;   
int pos = 0;   
String day={};
String mode={};
int m;
unsigned long k=0;
int thres=0;


void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  pinMode(3,OUTPUT);
  pinMode(4,OUTPUT);
  pinMode(13,OUTPUT);
  pinMode(2,OUTPUT);
  digitalWrite(2,HIGH);
   myservo.attach(9); 
 
}

void loop() {
  // Read data from serial port and save it in "mode"
  if(Serial.available()>0){
    mode=Serial.readString();
    delay(100);
   for(int i=0;i<mode.length();i++){
      delay(100);
      EEPROM[i]=mode[i];
      
    }
   
  }
    if(! mode.length()>0 ){
   char n;
     for(int i=0;i<20;i++){
    delay(100);
     n=EEPROM[i];
   mode+=n;
  }
    }
     if(mode.substring(0,2)=="05"){
      thres=512;
    }else if(mode.substring(0,2)=="10"){
      thres=340;
    }else if(mode.substring(0,2)=="15"){
      thres=250;
    }
    
   
  if(analogRead(A0)<=thres){
    k=0;
    day="DAY";
  }else{day="NIGHT";
   }
   
   Serial.println(mode);

  if(mode.substring(6,8)=="ON"){
    if(k==0&&analogRead(A0)>=thres){
       for(pos = 0; pos <= 180; pos++) 
  {                                   
    myservo.write(pos);               
    delay(15);                       
  } 
  for(pos = 180; pos>=0;pos--)      
  {                                
    myservo.write(pos=0); 

}
      
    }
    k=1;if(analogRead(A0)>=thres){
       digitalWrite(3,HIGH);
    digitalWrite(4,HIGH);
    digitalWrite(13,HIGH);
    }else{
       digitalWrite(3,LOW);
    digitalWrite(4,LOW);
    digitalWrite(13,HIGH);
    }
   
    }else if(mode.substring(6,9)=="OFF"){
      digitalWrite(3,LOW);
      digitalWrite(4,LOW);
      digitalWrite(13,LOW);
    }else if(mode.substring(6,10)=="AUTO"){
      //if it is day time switch off all lights
      if(day=="DAY"){
        digitalWrite(3,LOW);
      digitalWrite(4,LOW);
      digitalWrite(13,LOW); 
      
      }else if((day=="NIGHT")&&k==0){
         for(pos = 0; pos <= 180; pos++) {                                   
    myservo.write(pos);               
    delay(15);                       
  } 
 for(pos = 180; pos>=0;pos--)      
  {                                
    myservo.write(pos=0); 

}
      
         digitalWrite(3,HIGH);
      digitalWrite(4,HIGH);
      digitalWrite(13,HIGH);
      
      while(!(Serial.available()||analogRead(A0)<=thres)){
      m=mode.substring(10,11).toInt();
      if(k==m*10){
        digitalWrite(3,LOW);
       
        
        break;
      }
      delay(1000);
      k++;  
      }
      }
      
    }else if(mode.substring(6,12)=="MANUAL"&&mode.substring(12,19)=="Phase_A"){
      digitalWrite(3,HIGH);
      digitalWrite(4,LOW);
      digitalWrite(13,LOW);
    }
    else if(mode.substring(6,12)=="MANUAL"&&mode.substring(12,19)=="Phase_B"){
       digitalWrite(4,HIGH);
      digitalWrite(3,LOW);
      digitalWrite(13,HIGH);
}
}


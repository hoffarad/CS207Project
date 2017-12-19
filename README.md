# CS207Project
Shoebox DMX Console 

/*
  Shoe box DMX console
  
  control LED DMX stage lights with buttons and sliders using dmxSimple protocols and max485 intergrated curcuit.
  - displays mode number on screen.
   

  code modified from lab code created by
  Alex Clarke 
  and example code from spark from micro Oled breakout screen 

   modified by 
   Adam Hoffart 
   DEC 2017 
  
 */

#include <DmxSimple.h>
#include <SPI.h>
#include <Wire.h>
#include <SFE_MicroOLED.h>
//////////////////////////
// MicroOLED Definition //
//////////////////////////
#define PIN_RESET 9  // Connect RST to pin 9
#define PIN_DC    8  // Connect DC to pin 8
#define PIN_CS    10 // Connect CS to pin 10
#define DC_JUMPER 0

//////////////////////////////////
// MicroOLED Object Declaration //
//////////////////////////////////
MicroOLED oled(PIN_RESET, PIN_DC, PIN_CS); // SPI declaration
//MicroOLED oled(PIN_RESET, DC_JUMPER);    // I2C declaration

int SCREEN_WIDTH = oled.getLCDWidth();
int SCREEN_HEIGHT = oled.getLCDHeight();


float curButton = HIGH; // mode up
float lastButton = HIGH;
const int button = 2;


float curButton2 = HIGH; // mode down
float lastButton2 = HIGH;
const int button2 = 1;

float curButton3 = HIGH; // red button
float lastButton3 = HIGH;
const int button3 = 4;

float curButton4 = HIGH; //green button
float lastButton4 = HIGH;
const int button4 = 12;

float curButton5 = HIGH; // blue button
float lastButton5 = HIGH;
const int button5 = 7;

float curButton6 = HIGH; // white buttom
float lastButton6 = HIGH;
const int button6 = 5;

float curButton7 = HIGH; // yellow button
float lastButton7 = HIGH;
const int button7 = 6;

int ledMode = 1; // chase mode cycle 
int RedMode = 1; // red colour cycle
int GrnMode = 1; // green colour cycle
int BluMode = 1; // blue colour cycle
int WitMode = 1; // turn on all leds - control by slide2,3,and 4. 
int YlwMode = 1; // turn off/on all lights 

void setup() {
  // put your setup code here, to run once:
  DmxSimple.usePin(3);

  /* DMX devices typically need to receive a complete set of channels
  ** even if you only need to adjust the first channel. You can
  ** easily change the number of channels sent here. If you don't
  ** do this, DmxSimple will set the maximum channel number to the
  ** highest channel you DmxSimple.write() to. */
  //DmxSimple.maxChannel();

  Serial.begin(9600);

  pinMode(button, INPUT);
  pinMode(button2, INPUT);
  pinMode(button3, INPUT);
  pinMode(button4, INPUT);
  pinMode(button5, INPUT);
  pinMode(button6, INPUT);
  pinMode(button7, INPUT);
  // Trick to make this a pull up button
  // Other side of button must be attached to ground
  // This trick doesn't work on pin 13.
  digitalWrite(button, HIGH);
  digitalWrite(button2, HIGH);
  digitalWrite(button3, HIGH);
  digitalWrite(button4, HIGH);
  digitalWrite(button5, HIGH);
  digitalWrite(button6, HIGH);
  digitalWrite(button7, HIGH);

  oled.begin();
  oled.clear(ALL);
  oled.display();

  oled.begin();

}


void loop() {

  int slide1 = A0;
  int slide2 = A1;
  int slide3 = A2;
  int slide4 = A3;

  lastButton = curButton;
  curButton = digitalRead(button);

  lastButton2 = curButton2;
  curButton2 = digitalRead(button2);

  lastButton3 = curButton3;
  curButton3 = digitalRead(button3);

  lastButton4 = curButton4;
  curButton4 = digitalRead(button4);

  lastButton5 = curButton5;
  curButton5 = digitalRead(button5);

  lastButton6 = curButton6;
  curButton6 = digitalRead(button6);

  lastButton7 = curButton7;
  curButton7 = digitalRead(button7);

  //int spd= analogRead(slide1);
  //spd = map(spd, 0, 1023, 244, 255); // mapping to the speed cycle of chase

  int dim1 = analogRead(slide2);
  dim1 = map(dim1, 0, 1023, 0, 255); // mapping the brightness of the red leds

  int dim2 = analogRead(slide3);
  dim2 = map(dim2, 0, 1023, 0, 255); // mapping the brightness of the green leds

  int dim3 = analogRead(slide4);
  dim3 = map(dim3, 0, 1023, 0, 255); // mapping the brightness of the blue leds


  if (curButton == LOW && lastButton == HIGH)

  {
    // if it is, count a press - cycle through chase modes
    ledMode = ledMode + 1;

    // if we've counted to an invalid mode
    if (ledMode == 17)
    {
      // reset to mode 1
      ledMode = 1;
    }
  }


  if (curButton2 == LOW && lastButton2 == HIGH)

  {
    // if it is, count a press
    ledMode = ledMode - 1;

    // if we've counted to an invalid mode
    if (ledMode == 0)
    {
      // reset to mode 1
      ledMode = 16;
    }
  }


  // Detect the current mode and set the LEDs appropriately
  if (ledMode == 1) // Mode 1 - colour fade
  {

    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 40, 69); // map chase speed - give values for colour fade chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #1"); // display mode #1
    //oled.println ("COLOUR FADE");
    oled.display(); // Draw to the screen
    //delay(1000);

  }
  else if (ledMode == 2)
  {
    //Mode 2 - colour flash
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 70, 99); // map chase speed - give values for colour flash chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #2"); /// display mode #2
    //oled.println ("COLOUR FLASH");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 3)
  {
    //Mode 3 - purple chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 100, 111); // map chase speed - give values for purple chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #3"); // display mode #3
    //oled.println ("PURPLE CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 4)
  {
    //Mode 4 - red chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 112, 123); // map chase speed - give values for red chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #4"); // display mode #4
    //oled.println ("RED    CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 5)
  {
    //Mode 5 - green and red chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 124, 135); // map chase speed - give values for green and red chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #5"); // display mode #5
    //oled.println ("GRN+RED CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 6)
  {
    //Mode 6 - blue and green chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 136, 147); // map chase speed - give values for blue and green chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #6"); /// display mode #6
    //oled.println ("BLU+GRN CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 7)
  {
    //Mode 7 - yellow and purple chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 148, 159); // map chase speed - give values for yellow and purple chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #7"); // display mode #7
    //oled.println ("YLW+PRP CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 8)
  {
    //Mode 8 - yellow and red chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 160, 171); // map chase speed - give values for yellow and red chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #8"); // display mode #8
    //oled.println ("YLW+RED CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 9)
  {
    //Mode 9 - pink and green chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 172, 183); // map chase speed - give values for pink and green chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE #9"); // display mode #9
    //oled.println ("PNK+GRN FLASH");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 10)
  {
    //Mode 10 - red and green chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 184, 195); // map chase speed - give values for red and green chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE 10"); // display mode #10
    //oled.println ("RED+GRN FLASH");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 11)
  {
    //Mode 11 - blue chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 196, 207); // map chase speed - give values for blue chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE 11"); // display mode #11
    //oled.println ("BLUE   CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 12)
  {
    //Mode 12 - multi colour chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 208, 219); // map chase speed - give values for multi colour chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE 12"); // display mode #12
    //oled.println ("MLT CLR CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 13)
  {
    //Mode 13 - multi colour chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 220, 231); // map chase speed - give values for multi colour chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen


    oled.print( "MODE 13"); // display mode #13
    //oled.println ("MLT CLR CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 14)
  {
    //Mode 14 - multi colour chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 232, 243); // map chase speed - give values for multi colour chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen


    oled.print( "MODE 14"); // display mode #14
    //oled.println ("MLT CLR CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else if (ledMode == 15)
  {
    //Mode 15 - multi colour chase
    int spd = analogRead(slide1);
    spd = map(spd, 0, 1023, 244, 255); // map chase speed - give values for multi colour chase
    DmxSimple.write (1, spd);

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (0, 0); // set point for text on screen

    oled.print( "MODE 15"); // display mode #15
    //oled.println ("MLT CLR CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }
  else
  {
    //Mode 16 - off mode

    DmxSimple.write (1, 0); // turn off chase mode

    oled.clear(PAGE); // clear screen
    oled.setFontType(1); // smaller font size
    oled.setCursor (20, 0); // set point for text on screen - displays text in the middle


    oled.print( "OFF" ); // display off
    //oled.println ("MULTI CLR CHASE");
    oled.display(); // Draw to the screen
    //delay(1000);
  }


  if (curButton3 == LOW && lastButton3 == HIGH)


  {
    // if it is, count a press
    RedMode = RedMode + 1;

    // if //we've counted to an invalid mode
    if (RedMode == 4)
    {
      //reset to mode 1
      RedMode = 1;
    }
  }

  if (RedMode == 1)
  {
    DmxSimple.write (3, 0); //turn off green LEDs
    DmxSimple.write (4, 0); //turn off blue LEDs
    DmxSimple.write (2, 0); //turn off red LEDs
  }

  else if  (RedMode == 2)
  {
    DmxSimple.write (2, 255); // turn on red LEDs
    DmxSimple.write (4, 0); // turn off blue LEDs
    DmxSimple.write (3, 0); // turn off green LEDs
  }

  else

  {
    DmxSimple.write (2, 255); // turn on red LEDs
    DmxSimple.write (3, 255); // turn on green LEDs to make color yellow
    DmxSimple.write (4, 0); // turn off blue LEDs
  }


  if (curButton4 == LOW && lastButton4 == HIGH)

  {
    // if it is, count a press
    GrnMode = GrnMode + 1;

    // if //we've counted to an invalid mode
    if (GrnMode == 4)
    {
      //reset to mode 1
      GrnMode = 1;
    }
  }

  if (GrnMode == 1)
  {
    //DmxSimple.write (3, 0); // do nothing
    //DmxSimple.write (4, 0);
    //DmxSimple.write (2, 0);
  }

  else if  (GrnMode == 2)

  {
    DmxSimple.write (3, 255); // turn on green LEDs
    DmxSimple.write (4, 0); // turn off blue LEDs
    DmxSimple.write (2, 0); // turn off red LEDs
  }

  else

  {
    DmxSimple.write (3, 255); // turn on green LEDs
    DmxSimple.write (4, 255); // turn on blue LEDs to make the colour teal
    DmxSimple.write (2, 0); // turn off red LEDs
  }



  if (curButton5 == LOW && lastButton5 == HIGH)

  {
    // if it is, count a press
    BluMode = BluMode + 1;

    // if we've counted to an invalid mode
    if (BluMode == 4)
    {
      //reset to mode 1
      BluMode = 1;
    }
  }
  if (BluMode == 1)
  {
    //DmxSimple.write (3, 0); // do nothing
    //  DmxSimple.write (4, 0);
    //  DmxSimple.write (2, 0);


  }
  else if  (BluMode == 2)
  {
    DmxSimple.write (4, 255); // turn on blue LEDs
    DmxSimple.write (2, 0); // turn off red LEDs
    DmxSimple.write (3, 0); // turn off green LEDs


  }
  else
  {
    DmxSimple.write (4, 255); // turn on blue LEDs
    DmxSimple.write (2, 255); // turn on red LEDs to make colour purple
    DmxSimple.write (3, 0); // turn off green LEDs
  }


  if (curButton6 == LOW && lastButton6 == HIGH)

  {
    // if it is, count a press
    WitMode = WitMode + 1;

    // if we've counted to an invalid mode
    if (WitMode == 3)
    {
      //reset to mode 1
      WitMode = 1;
    }
  }


  if (WitMode == 1)

  {
    //DmxSimple.write (3, 0); // do nothing
    // DmxSimple.write (4, 0);
    // DmxSimple.write (2, 0);
  }

  else // if  (WitMode == 2)

  {
    DmxSimple.write (2, dim1); // turn on red LEDs and fade intensity with slide 2
    DmxSimple.write (3, dim2); // turn on red LEDs and fade intensity with slide 2
    DmxSimple.write (4, dim3); // turn on red LEDs and fade intensity with slide 2
    delay(10);
  }

  // else
  {
    //  DmxSimple.write (3, dim2); // do nothing
    // DmxSimple.write (4, dim3);
    // DmxSimple.write (2, dim1);
  }


  if (curButton7 == LOW && lastButton7 == HIGH)

  {
    // if it is, count a press
    YlwMode = YlwMode + 1;

    // if we've counted to an invalid mode
    if (YlwMode == 3)
    {
      //reset to mode 1
      YlwMode = 1;
    }
  }

  if (YlwMode == 1) // do nothing
  {
    //DmxSimple.write (3, dim2);
    //DmxSimple.write (4, dim3 );
    //DmxSimple.write (2, dim1);
  }

  else

  {
    DmxSimple.write (3, 0); // turn off green LEDs
    DmxSimple.write (4, 0); // turn off blue LEDs
    DmxSimple.write (2, 0); // turn off red LEDs
    DmxSimple.write (1, 0); // turn off chase modes
    delay(10);
  }

}

# Sonar Visualizer
<!--

Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!
You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:

<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
-->

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Jayden Z. | The Bronx High School of Science | Aerospace Engineering | Rising Sophmore

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/i76NpsIv7Yo?si=w8wpN9gurx0XRQlu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE


<!--
# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 
-->

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/hcZaw2V2fvw?si=IJPlOZxOlMRwfAQd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
This is the program loaded onto the Teensy 4.0 MCU using PlatformIO IDE on VSCode 

```c++
#include <Arduino.h>
/*
https://www.hackster.io/faweiz/arduino-radar
Radar Screen Visualisation for HC-SR04
Sends sensor readings for every degree moved by the servo
values sent to serial port to be picked up by Processing
*/

#include <PWMServo.h> 
 
#define TRIGGER_PIN  2   // Arduino pin 2 tied to trigger pin on the ultrasonic sensor.
#define ECHO_PIN     3   // Arduino pin 3 tied to echo pin on the ultrasonic sensor.
#define MAX_DISTANCE 150 // Maximum distance we want to ping for (in centimeters). Maximum sensor distance is rated at 400-500cm.
#define SERVO_PWM_PIN 4 //set servo to Arduino's pin 9
 
// means -angle .. angle
#define ANGLE_BOUNDS 80
#define ANGLE_STEP 3
 
int angle = 0;
 
// direction of servo movement 
// -1 = back, 1 = forward 
int dir = 1;

float duration;

PWMServo myservo;
 
void setup() {
  pinMode(TRIGGER_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  Serial.begin(9600); // initialize the serial port:
  myservo.attach(SERVO_PWM_PIN); //set servo to Arduino's pin 9
}

void getDistanceAndSend2Serial(int angle) {
  digitalWrite(TRIGGER_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIGGER_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIGGER_PIN, LOW);
  int range = analogRead(23);
  //float timeout = 3.92 * (float)range;
  //Serial.print(range);
  //Serial.print("/n");
  duration = pulseIn(ECHO_PIN, HIGH, 1500 * range / 325);
  long cm = duration / 58;
  Serial.print(angle, DEC);
  Serial.print(",");
  Serial.println(cm, DEC);
  delay(1);
}

void loop() {
 
  delay(50);
  // we must renormalize to positive values, because angle is from -ANGLE_BOUNDS .. ANGLE_BOUNDS
  // and servo value must be positive
  myservo.write(angle + ANGLE_BOUNDS);
  
  // read distance from sensor and send to serial
  getDistanceAndSend2Serial(angle);
  
  // calculate angle 
  if (angle >= ANGLE_BOUNDS || angle <= -ANGLE_BOUNDS) {
    dir = -dir;
  }
  angle += (dir * ANGLE_STEP);  
}
```

This is the program loaded onto Processing 4.3

```java
/*
 // https://github.com/faweiz
 // https://portfolium.com/faweiz
 // https://www.linkedin.com/in/faweiz
 // Project Tutital : https://www.hackster.io/faweiz/arduino-radar
 
Radar Screen Visualisation for Sharp HC-SR04
Maps out an area of what the HC-SR04 sees from a top down view.
Takes and displays 2 readings, one left to right and one right to left.
Displays an average of the 2 readings
*/

import processing.serial.*;
 
int SIDE_LENGTH = 1000;
int ANGLE_BOUNDS = 80;
int ANGLE_STEP = 3;
int HISTORY_SIZE = 10;
int POINTS_HISTORY_SIZE = 500;
int MAX_DISTANCE = 50;
 
int angle;
int distance;

/* choose either color or background image */
color bgcolor = color (0,0,0);
//PImage bgimage = loadImage("test.png");
 
int radius;
float x, y;
float leftAngleRad, rightAngleRad;
 
float[] historyX, historyY;
Point[] points;
 
int centerX, centerY;
 
String comPortString;
Serial myPort;
 
void setup() {
  size(1000, 500, P2D);
  noStroke();
  //smooth();
  rectMode(CENTER);
 
  radius = SIDE_LENGTH / 2;
  centerX = width / 2;
  centerY = height;
  angle = 0;
  leftAngleRad = radians(-ANGLE_BOUNDS) - HALF_PI;
  rightAngleRad = radians(ANGLE_BOUNDS) - HALF_PI;
 
  historyX = new float[HISTORY_SIZE];
  historyY = new float[HISTORY_SIZE];
  points = new Point[POINTS_HISTORY_SIZE];
 
  myPort = new Serial(this, "COM6", 9600);
  myPort.bufferUntil('\n'); // Trigger a SerialEvent on new line
}
 
 
void draw() {
 
/* choose either bgcolor or bgimage */
  
  background(bgcolor);

  drawRadar();
  drawFoundObjects(angle, distance);
  drawRadarLine(angle);

 /* draw the grid lines on the radar every 30 degrees and write their values 180, 210, 240 etc.. */
  for (int i = 0; i <= 6; i++) {
    strokeWeight(1);
    stroke(0,255,0);
    line(radius, radius, radius + cos(radians(180+(30*i)))*SIDE_LENGTH/2, radius + sin(radians(180+(30*i)))*SIDE_LENGTH/2);
    fill(255, 255, 255);
    noStroke();
    text(Integer.toString(0+(30*i)), radius + cos(radians(180+(30*i)))*SIDE_LENGTH/2, radius + sin(radians(180+(30*i)))*SIDE_LENGTH/2, 25, 50);
  }

/* Write information text and values. */
  noStroke();
  fill(0);
int Degrees=0;

if (angle>0 & angle <80)
{
  Degrees = angle+100;
}
else if (angle<0 & angle >-80)
{
  Degrees = angle+80;
}

  fill(0, 300, 0);
  text("Degrees: "+Integer.toString(Degrees), 100, 460, 100, 50);   text("degree", 200, 460, 100, 50);      // use Integet.toString to convert numeric to string as text() only outputs strings
  text("Subject Distance: "+Integer.toString(distance / 10), 100, 480, 200, 30);  text("cm", 200, 490, 100, 50);       // text(string, x, y, width, height)
  text("Radar screen code at www.Faweiz.com/radar", 900, 480, 250, 50);
  
  text("0", 620, 500, 250, 50);
  text("10 cm", 600, 420, 250, 50);

  text("20 cm", 600, 320, 250, 50);

  text("30 cm", 600, 220, 250, 50);
  
  text("40 cm", 600, 120, 250, 50);
  
  text("50 cm", 600, 040, 250, 50);
   
  noFill();
  rect(70,60,200,200);
  fill(0, 250, 0); 
  text("Screen Key:", 100, 50, 150, 50);
  fill(0,50,0);
  rect(30,53,10,10);
  text("Very Weak", 115, 70, 150, 50);
  fill(0,110,0);
  rect(30,73,10,10);
  text("Strong", 115, 90, 150, 50);
  fill(0,170,0);
  rect(30,93,10,10);
  text("Very Strong", 115, 110, 150, 50);
}
 
void drawRadarLine(int angle) {
 
  float radian = radians(angle);
  x = radius * sin(radian);
  y = radius * cos(radian);
 
  float px = centerX + x;
  float py = centerY - y;
 
  historyX[0] = px;
  historyY[0] = py;
  
  int Degrees=0;
  if (angle>0 & angle <80)
  {
    Degrees = angle+100;
  }
  else if (angle<0 & angle >-80)
  {
    Degrees = angle+80;
  }
   
   //get rgb color from http://www.rapidtables.com/web/color/RGB_Color.htm
    fill(0, 153, 0); 
    
    float b = centerY;
    
    arc(centerX,centerY,SIDE_LENGTH, SIDE_LENGTH,radians(angle-100),radians(angle-90));
        
    shiftHistoryArray();
}
 
void drawFoundObjects(int angle, int distance) {
 
  if (distance > 0) {
    float radian = radians(angle);
    x = distance * sin(radian) ;
    y = distance * cos(radian) ;
 
    int px = (int)(centerX + x);
    int py = (int)(centerY - y);
 
    points[0] = new Point(px, py);
  }
  else {
    points[0] = new Point(0, 0);
  }
  for (int i=0;i<POINTS_HISTORY_SIZE;i++) {
 
    Point point = points[i];
  
    if (point != null) {
 
      int x = point.x;
      int y = point.y;
 
      if (x==0 && y==0) continue;
 
      int colorAlfa = (int)map(i, 0, POINTS_HISTORY_SIZE, 20, 0);
      int size = (int)map(i, 0, POINTS_HISTORY_SIZE, 30, 5);
 
      fill(0, 255, 0, colorAlfa);
      noStroke();
      ellipse(x, y, size, size);
    }
    
    fill(255, 0, 0);
  }
  
 shiftPointsArray();
  
}
 
void drawRadar() {
  stroke(0,255,0);
  noFill();
  // make 5 circle with 50mm each
  for (int i = 0; i <= (SIDE_LENGTH / 200); i++) {
    arc(centerX, centerY, 200 * i, 200 * i, leftAngleRad, rightAngleRad);
  }
}                              
 
void shiftHistoryArray() {
 
  for (int i = HISTORY_SIZE; i > 1; i--) {
 
    historyX[i-1] = historyX[i-2];
    historyY[i-1] = historyY[i-2];
  }
}
 
void shiftPointsArray() {
 
  for (int i = POINTS_HISTORY_SIZE; i > 1; i--) {
 
    Point oldPoint = points[i-2];
    if (oldPoint != null) {
 
      Point point = new Point(oldPoint.x, oldPoint.y);
      points[i-1] = point;
    }
  }
}
 
void serialEvent(Serial cPort) {
 
  comPortString = cPort.readStringUntil('\n');
  if (comPortString != null) {
 
    comPortString=trim(comPortString);
    String[] values = split(comPortString, ',');
    
    try {
    
      angle = Integer.parseInt(values[0]);
      distance = int(map(Integer.parseInt(values[1]), 1, MAX_DISTANCE, 1, radius));   
    } catch (Exception e) {}
  }
}
 
class Point {
  int x, y;
 
  Point(int xPos, int yPos) {
    x = xPos;
    y = yPos;
  }
 
  int getX() {
    return x;
  }
 
  int getY() {
    return y;
  }
}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Elegoo UNO R3 Super Starter Kit | What the item is used for | $Price | <a href="https://www.amazon.com/ELEGOO-Project-Tutorial-Controller-Projects/dp/B01D8KOZF4/ref=sr_1_3_pp?crid=2CDRMQVSYOD4H&dib=eyJ2IjoiMSJ9.AcWZy-Yg4mDTnhzEHozxzPZdVC5-KUL2tW-OQewDKpA7ZhEVnlvYJFELmn1cEN5uvrZVxp4St_nlIhbtJibvxUj7s5mZJHZ5gTUoGHyjSCEJnV1m-a2PWxrXyWYqZrufz70WGPo3NV3-f7iFEXccDbUvJu8BRvjxPCjgkJ_uJnDxpPk3Gjw_yw7XMWWb8ll4GsOLKWrxeEr1uOS5BeD0EU2Y1MvZXAOcITMnykL7K69Fr4P6SnWB2EpkaJ37raPdYGD096Z_rSPyEsRfxIx6Rv9N65w4nRID980vI94aLYQ.PuIIln1now7ULLLgzCb0zPl5SBpDXd-IT05ToFwhflM&dib_tag=se&keywords=arduino+starter+kit&qid=1720652047&s=industrial&sprefix=arduino+starter+ki%2Cindustrial%2C72&sr=1-3"> Link </a> |
| Ultrasonic Sensor + Mount | What the item is used for | $Price | <a href="https://www.amazon.com/Ultrasonic-Mounting-Distance-Compatible-Duemilanove/dp/B0C5JJV53K/ref=asc_df_B0C5DGD7S1/?tag=hyprod-20&linkCode=df0&hvadid=693270340479&hvpos=&hvnetw=g&hvrand=7779174236092318711&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9005936&hvtargid=pla-2202729908763&mcid=3bf1e1f77dcb3a67b63221150e0cbd5f&gad_source=1&th=1"> Link </a> |
| 9V Batteries | What the item is used for | $Price | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/ref=sr_1_5_pp?crid=3TQ7ANPH958JM&dib=eyJ2IjoiMSJ9.bmcV2Upj_vpB6G9CFlPPxYAryat512da7ekZjc52HecXSTmtx7PbJ50EgQFPCMqlAxjOUq-tL4vQTpozlHvH89bMwx-HJoyGcdz6EY8HrMxahTiqOXkoP7ewkDcgHoMhmHamdlQfW6FBHO0Gm-DYZZnnMuvEU3qOpemA8PGEvRhEx4-lGaBZhrvls039G1-9SizAW-YRGXZ2fFrdVDlREyyOhAuxXZaE5QqUxWesRQgP9UfGOYaInRWTTPwhDbXFa-RPzGbU1C_u4wq-NMqKBtWEQqR9-cA8O3FYOx3icEY.dtKJmI2T-iCmMM_bYnbiHUWzhKpJDRxS-bBmZIwYFKM&dib_tag=se&keywords=9v+batteries&qid=1720651326&rdc=1&s=electronics&sprefix=9v+batteries%2Celectronics%2C105&sr=1-5"> Link </a> |

<!--# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
-->

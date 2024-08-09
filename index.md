# Sonar Visualizer
<!--

Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!
You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:

<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
The Sonar Visualizer is a two-piece project that consists of a sensor package and computer code transferring data through serial communication. The ultrasonic sensor is mounted on a servo that turns it left and right to cover a larger area, and the code on the computer uses data from the sensor package to create a visualizer for the scan area.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Jayden Z. | The Bronx High School of Science | Aerospace Engineering | Rising Sophmore

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/i76NpsIv7Yo?si=w8wpN9gurx0XRQlu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My final milestone consists of two modifications: range control using a potentiometer and an enclosure modeled using Fusion360. The potentiometer I used was a B103 potentiometer, and by printing out the values received from the potentiometer, I found that the values ranged from 1 to 1024. I used the values of the potentiometer as a multiplier for the timeout duration of the ultrasonic sensor, which controls the max distance the ultrasonic sensor looks at before defaulting to a value of 0. While the range of the potentiometer is large enough to give more than enough resolution for this project, I also realized that this range so large that I ended up having troubles with the max range of the ultrasonic sensor being too high. Furthermore, I was finding that the sensor would not detect anything in the lower half of the potentiometer's input range. After some testing, I determined that the ultrasonic sensor has a minimum timeout value of around 2000 microseconds, below which it refuses to work. In the end, I started out the timeout walue with 1000 microseconds and added the value received from the potentiometer multiplied 1.85 such that the maximum timeout duration would result in a distance of 50 cm. I am disappointed in the results of this modification, however the adjustments of the range would be finer if the overall maximum range were greater.

I was not able to finish modelling the enclosure for the sensor package on fusion360, however I did achieve significant progress on the modification. I decided on a simple square box design because I didn't need it to be very flashy. I made a raised section of the case to support the MCU in a rear corner of the case, and I made a mounting hole for the servo in the center of the case. I wanted toi include a bearing to support the servo shaft that would need to be extended to reach outside ofthe case, so I made a larger hole in the lid. I needed a way to make sure the servo shaft does not fall out, so I used the flange of the bearing to my advantage. I positioned the bearing so that the flange is on the inside of the case, and I created a flange on the bottom of the shaft, underneath the bearing. The axle flange holds the axle in place, and the bearing flange holds the bearing in place, and the lid secures everything. Because the lid was supporting the shaft, I added multiple M3 screws to hold it to the case. I have not completed the ultrasonic sensor mount, but that is definitely planned in the future.

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

My project is the Sonar Visualizer with the HC-SR04 Ultrasonic Sensor. This project consists of an ultrasonic sensor mounted on a sweeping servo that sends  data to a computer, where a second program takes the data and plots it on a seperate window. A microcontroller acts as an intermediary between the sensor and the computer, converting the raw data from the ultrasonic sensor to distance data that can be plotted by the computer. The microcontroller then sends a command to the servo to turn the ultrasonic sensor to a different angle to get a new reading. One large challenge I faced during this stage of the project was getting the program on the computer to run successfully. The original project author gave little instruction on how to set up the program, so I had to search the internet to find the correct platform to run the program in. After installing Processing 4.3 to run the code, I ran into some issues with unused variables, which I solved after going through the code and removing the problematic variables. After this stage, the base project began working perfectly, so I moved on to my first modification which was swapping the microcontroller from the Arduino Uno R3 to the Teensy 4.0. I encountered my second major obstacle here, as the change in MCUs caused all of the libraries in the code to break. I ended up solving the issue by replacing the servo library with one that supported the Teensy 4.0 and writing the code for the ultrasonic sensor by hand. 

Next, I would like to add user inputs to control some aspect of the sensor, like the range, sweep range, or the angle of the center point. I also want to design and 3D print a case for the sensor so everything is contained in a compact package. Another possible modification would be increasing the maximum range of the sensor to read a larger area.

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
  duration = pulseIn(ECHO_PIN, HIGH, 1000 + range * 1.85);
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

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Elegoo UNO R3 Super Starter Kit | Breadboard, Jumper Wires, Ultrasonic Sensor, Potentiometer, Servo | $44.99 | <a href="https://www.amazon.com/ELEGOO-Project-Tutorial-Controller-Projects/dp/B01D8KOZF4/ref=sr_1_3_pp?crid=2CDRMQVSYOD4H&dib=eyJ2IjoiMSJ9.AcWZy-Yg4mDTnhzEHozxzPZdVC5-KUL2tW-OQewDKpA7ZhEVnlvYJFELmn1cEN5uvrZVxp4St_nlIhbtJibvxUj7s5mZJHZ5gTUoGHyjSCEJnV1m-a2PWxrXyWYqZrufz70WGPo3NV3-f7iFEXccDbUvJu8BRvjxPCjgkJ_uJnDxpPk3Gjw_yw7XMWWb8ll4GsOLKWrxeEr1uOS5BeD0EU2Y1MvZXAOcITMnykL7K69Fr4P6SnWB2EpkaJ37raPdYGD096Z_rSPyEsRfxIx6Rv9N65w4nRID980vI94aLYQ.PuIIln1now7ULLLgzCb0zPl5SBpDXd-IT05ToFwhflM&dib_tag=se&keywords=arduino+starter+kit&qid=1720652047&s=industrial&sprefix=arduino+starter+ki%2Cindustrial%2C72&sr=1-3"> Link </a> |
| USB-A to Micro USB Cable | Connection Cable | $6.65 | <a href="https://www.amazon.com/Amazon-Basics-Charging-Transfer-Gold-Plated/dp/B07232M876/ref=sr_1_3?crid=30DKHNAPFKM58&dib=eyJ2IjoiMSJ9.Bni23SC_G-PHVtUTSFFP-2wKzaGj-ZTnOQr9dVGB1VLChiOn4BJph48eyi2KHJLXeXgQQamW-HzkBoHHalD8zKK59DJWaqjYCDRkjQ1NlUhPnjxZDkOWv3bHu3d6qfLQv8tCm-iJ-5w6btOeofmNjgwMAMDHcKZ7PyZwt2iAii1zIqRZfasHAs66d6rhsGmI9eb1_7aOjoGxh8-FEwMHfXBUoTlUMN7YF9dmIuiMQDhIoTTXOXPtwVhwDib_HesxEU5nIY5pciVRQtAoSu_6nnN28CCi7jmXobS-NRhTzGc.wq-p_1yQPvreRz21RXHw-KuABvRnV5hAPB5-GnPxkSM&dib_tag=se&keywords=micro+usb+to+usb+a&qid=1723217132&s=industrial&sprefix=micro+usb+to+usb+a%2Cindustrial%2C84&sr=1-3"> Link </a> |
| Teensy 4.0 | Processing and Serial Communication | $34.60 | <a href="https://www.amazon.com/Teensy-4-0-With-Pins/dp/B08259KDHY/ref=sr_1_1?dib=eyJ2IjoiMSJ9.Y4QlCmOasCI9nmyZhTlXaaS5DJWsh5GZ48biRloYKX0Pa8T8uxRvW2PYhS7cC_ZPapnaIm1PnvJ1Y7QrPrwxQAtkcNhf-BSIN52lRLXZl48ViDH-MnRu6DDqGKbrvqJbSF1CDjLCoOW3JCzCymseE9SjYLEWMrcrIhGKYHdgnbn1WOQM5fMXiwZ24lop1XEPw5GyHs6zqL8EWen1w2vHwsf8sUUq21W42-EJxeN7Q0c.Eup7tI6qSJQZEyomVBuieS4u-J0XVLuH9AO-xKhYt50&dib_tag=se&keywords=teensy+4.0&qid=1723216960&sr=8-1"> Link </a> |

Note: The only componentrs required from the Elegoo kit are the HC-SR04 Ultrasonic Sensor, B103 Potentiometer, SG-90 Servo, jumper wires, and breadboard.

<!--# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
-->

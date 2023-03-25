# WVR Glove Wiki v0.1
## Let´s start building the glove
### Things that you will need before we start building.

> **note** : I´m assuming that you have some basic stuff before hand 
> such as soldering equipment, 3D printer and some tools ... <br/>
> Also some knowledge in C++ would help you a bit.

> **info** : for newbies this could be a bit tough. I was thinking about doing a youtube tutorial. For that please vote [here]() if there is an interest for a video. 

* esp32 (wroom/wrover) Dev-board (I´m recommending that you get 2 of them )
* 1x 9 axis IMU (in this project a BNO055 was used)
* 5x sg90 Servos (this has to be some specific ons. see more in **modding servo**)
* 5x B10K [Potentiometer](https://www.amazon.com/TELESKY-Linear-Potentiometer-Variable-Resistors/dp/B09T32L928/ref=sr_1_1_sspa?crid=1ISH5MMM4R7PJ&keywords=b10k+potentiometer&qid=1679477752&s=industrial&sprefix=b10k+potentiometer%2Cindustrial%2C140&sr=1-1-spons&psc=1&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUEzTU9GQkVGTzBNNVNHJmVuY3J5cHRlZElkPUEwMjYxMTMyMUpBTTEzSDlIQU4xTyZlbmNyeXB0ZWRBZElkPUEwNTM1MDc1MzMxNjZHUlYzSFJZQiZ3aWRnZXROYW1lPXNwX2F0ZiZhY3Rpb249Y2xpY2tSZWRpcmVjdCZkb05vdExvZ0NsaWNrPXRydWU=) (also get them with some washer and nuts)
* some prototype pcb board (a 5x7 should be enough) <br/>
* bread board for testing purposes
#### optional stuff , but also recommended to have :
* 5x (10x) jst connector 5 pin both male (5x) and female (5x) (if you don't own any of it a jst tool kit would help you out)
* I also use **TPU** for printing the fingertips.


## Printing the part
Download the part from [this wiki]() repo.


## Modding SG90 servos
> **note** : This part could be somewhat complicated if you haven't work much in electronics. **But** it's not impossible. So let's go...

First **make sure** you get the right servo. What I mean by that is there are some **internal** differences depending on where you get them. 

What you will need is a servo with **2 chips** in it! <br/>
Next is to look up the part number on google. If google says that it is a **H-Bridge**  or a **H-Bridge motor control** then you are on the right track!  <br/>
In my case the **H-Bridge** is **TC8301**s and sometimes there could also  **TC118**s.


![TC8310 servo](https://user-images.githubusercontent.com/112129893/227542171-f93312a7-94da-40e7-ae0d-8eb50a54a8ef.jpg)


Now if we take a look at the data sheet of the **TC8301**. In the data sheet there is an example circuit diagram.
And we can see that pin **2** and **3** are labeled as input **A** and **B**.
With these 2 pins we can control the **speed** and **direction** with a **PWM** signal.  

![tc8301 circuit](https://user-images.githubusercontent.com/112129893/227541827-6f711e6a-ff27-4fcc-9017-f275cbe699e4.png)


Here is how we can control the motors **speed** and **direction**. Later on we control these input with a PWM signal.<br/> L = low <br/>
H = high<br/>
![TC8301 mode ](https://user-images.githubusercontent.com/112129893/227541836-4d4a9689-8cdd-423d-898c-729e9318463a.png)


In theory you can solder on the wires directly on the **TC8301** pins. A wire to INA and also a wire to INB, then also a wire to the middle pin of the potentiometer.
The yellow wire is no longer needed and can be removed.

And now you will have 5 wires attach to your servo. 
* INA wire
* INB wire 
* Potentiometer middle pin
* +VDD
* -GND

In my case I didn't do it that way. I flipped the PCB around and remove the other chip ("servo controller IC"). After some measuring I found out that there are pads connected to the **H-Bridge**. So I connected my wires there. But an additional wire connection is needed.
And of course there should be 5 wires coming out of the servo. 

> **tip** : take a note to your connected wires.
> And make **sure** that you cut a bigger exit for all the wires. 

> **caution!!** : there could be is a small extrusion/tip to prevent the servo from spinning 360°. This needs to be removed!

![modded Servo](https://user-images.githubusercontent.com/112129893/227541723-b24b6c59-6a42-4462-88d8-301991167e49.jpg)
> **note** : picture is mirrored/flipped <br/>
>Servo Pos = Potentiometer middle pin



## Preparing the ESP32
Get visual studio code and install plattformio.
Download the [firmware]() and add it to plattformio.<br/>
PlattformIO : Home>Projects>Add Existing

> **note** : The default serial bound rate in the firmware is set to 250000.<br/>
> This can be changed in src/Properties.h under ``#define BAUNDRATE`` 

Now just compile and upload the firmware to the esp32 to the right COM port with a baund rate of 115200. <br/>

> **note** : if you haven't changed the baund rate in the firmware please remember that you need set the bound rate in the serial monitor to 250000.



If you open up a serial monitor it should print out **" initialization error "** because no component are connected at the moment.






## Wiring 
Here is how every thing is wired internally.<br/>
I'm recommending that you prewire every thing on the breadboard first.<br/>
The IMU I'm using is the BNO055 from Adafruit.
![internal wiringpng](https://user-images.githubusercontent.com/112129893/227541600-a25ec624-8c0d-41ea-ad15-7f86e61eaa52.png)



## Testing
Now let's do some testing before you build the actual glove.
If every thing is connected correctly it should say **" initialize successful "** after boot up and you will get a lot of values that look somewhat like this.<br/>
![data_out_esp](https://user-images.githubusercontent.com/112129893/227541554-2f7a9a80-dd1f-4430-a277-ade7aacb6ba5.png)


And to test whatever the servo has been successfully modded you can send ``120;0;0;0;0`` to the eso32. This will turn on the the **thumb servo**. Or if you send ``-120;0;0;0;0`` then servo will turn the other way around. The larger the value, the faster it becomes. Max value is ``255``. The value for turning off the servo is ``0;0;0;0;0``. 
> **note** : **thumb** ; **index** ; **middle** ; **ring** ; >**pinky**


## Additional connection

To keep every thing nice and tight. I made a adapter board with JSTs, to put on top of the esp32.
![esp_adapterboard](https://user-images.githubusercontent.com/112129893/227541486-339af4a9-7b3f-4fc3-a10b-7b75a0ad3906.png)
![serco_jst](https://user-images.githubusercontent.com/112129893/227656085-f9b1b3ba-8475-497d-b635-c5ef67149b4a.png)


## Final look

![oneShape_model_v1](https://user-images.githubusercontent.com/112129893/227723656-6a1c3b95-7611-4abe-b45f-dd2d0282e889.PNG)


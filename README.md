# Timed_leds
A manual about timing and controlling a led strip with Arduino

# Components
- Node MCU (or any other arduino compatible iot platform)
- RGB LEDstrip
- USB cable to connect the Node MCU with your computer

# Step 1 - Connecting the components


- Connect the +5V wire to the 3V3 pin
- Connect the DIN wire to the D5 pin
- Connect the GND wire to the GND pin

# Step 2 - installing adafruit
Arduino has libraries with examples that enable you to get a lot of example code and we will use this to easily connect with our led strip.

If you don't have the arduino program you can download it here: https://www.arduino.cc/en/software 

- Now open the arduino program and go to Sketch  / include library / manage libraries…
- Search and install the latest version of  "Adafruit Neopixel".

# Step 3 - Let there be light
Now it's time to test the LEDstrips to see if you made any mistakes in the prior steps. You can see this as some sort of a “Checkpoint”.

- Go to File / Examples / Adafruit Neopixel / simple
- This will open up a file that has a bunch of code that controlls the LEDstrips. This code will be important later so try reading and understanding the code before editing it
- Once you understand the code you can edit it so it works with your setup, this is how my code looked like: 



```
#include <Adafruit_NeoPixel.h>

#define PIN        D5
#define NUMPIXELS 22
#define DELAYVAL 500

Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  pixels.begin();
}

void loop() {
  pixels.clear(); 

  for(int i=0; i<NUMPIXELS; i++) { 
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));
    pixels.show(); 
    delay(DELAYVAL);
  }
}
```

As you can see i places all the “#Define” code on top for easy editing and I changed the variables to fit with my setup.

## Why my code looks like this
- The PIN im using is D5, you should have this too because you changed it in step 1
- NUMPIXELS is the amount of pixels on your LEDstrip, for me this is 22 but it could be different for you.
- I also deleted the comments and some unnecessary code so I understand whats happening better.

## Lets test this!
- Save this file.
- Upload it to your device by clicking on the arrow button in the top left.
- And now watch arduino do its magic and turn on your leds.

If this is not working try the following:
- Wiring these things up is hard and its easy to make a mistake. Go back to your wires and check if every wire is in the right pin.
- check if you changed every #define.
- Check what the error says and try googling it.

# Step 4 - it's time to time
Now that we have working LEDs we need to control them based on a specific time. Unfortunately my Arduino does not have a built- in clock so we need to use another Library to time the activation of the LEDs. 

I order to time a function I used the “arduino-timer” library that has a example that makes the little light on the arduino blink so we can combine these 2 examples

Let's start with installing this library and opening the example.

- Go to Sketch  / include library / manage libraries…
- Search and install the latest version of  "arduino-timer".
- After you successfully installed this library go to file / examples / arduino-timer / blink.

Now that you have the blink example try reading and understanding this code too because we will be combining this code with the LED code.

After reading this code try uploading it and you will see that the little light on the arduino lights up for 1 second and stays off for 1 second.

# Step 5 - Combining the codes
Now it's time for the final clash of codes. As said before we will be combining the Adafruit Led code and the blink code to time the function that activated the LEDstrip. Let's first start with deleting the code that controls the blinking of the little led on the arduino. (make sure you dont delete the functions themselves)

```
#include <arduino-timer.h>

auto timer = timer_create_default();

bool toggle_led(void *) {

}

void setup() {

  timer.every(1000, toggle_led);
}

void loop() {
  timer.tick();
}
```

This is the bare minimum code we need and the code we ended up with. 

The next step is to insert the led code into the new blink code.

```
#include <arduino-timer.h>
#include <Adafruit_NeoPixel.h>

#define PIN        D5
#define NUMPIXELS 22
#define DELAYVAL 500

Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

auto timer = timer_create_default(); // create a timer with default settings

bool toggle_led(void *) {
  pixels.clear(); // ← this is weird
  for(int i=0; i<NUMPIXELS; i++) { 
    pixels.setPixelColor(i, pixels.Color(0, 150, 0));
    pixels.show(); 
    delay(DELAYVAL);
  }
  pixels.clear(); // ← this is weird
}

void setup() {
  pixels.begin();
  timer.every(5000, toggle_led);
}

void loop() {
  timer.tick();
}

```

This is the code I ended up with and worked for me but as you can see it has the pixels.clear() function twice because for some reason it would clear it with just one. Also i encountered a problem when i wanted to set the timer to more than 10 seconds because i exceeded the maximum storage space.

# Failed
So basically I succeeded in controlling the LEDs for 5 seconds and than clearing them and turning them on again for 5 seconds. However I still dont fully understand why i need to clear them 2 times and unfortunately for some reason putting the timer higher will ask too much from the arduino and results in errors.

# Sources
Arduino Time library - https://playground.arduino.cc/Code/Time/
Arduino forum - https://forum.arduino.cc/t/wanting-to-perform-actions-during-certain-times-of-day-how-does-one-code-this/452668 



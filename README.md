# led_nightlight
RGB LED strip nightlight project

I will attempt to outline how what I used to put together this project.

[![Watch the video](https://i.imgur.com/A6RZiVQ.png)](https://www.youtube.com/watch?v=DjNhkUHnM4E&feature=youtu.be)

The backbone of this is HomeAssistant.  If you don't have HA (Hassio specifically) then you need to!  HA provides the communication to the board that runs the strip as seen below.

![Imgur](https://i.imgur.com/QUKpijS.png)

The light is also able to be controlled via Google Home voice control, that's another story and a write up for a later date but you can find examples of how to do that on YT.

First off you need HomeAssistant installed.  I use Hassio to easily use add-ons and snapshots.  
https://www.homeassistant.io

The devboard I used is a NodeMCU, but any 8266 based board can be used.  They're cheap, 2 pack for like $12USD on Amazon.  There are numerous things you can do with just a board and a strand of lights.  There are a ton of great repos around here that people way smarter than I have put together (JasonCoon, McLighting, WLED).  I plugged the board in to a breadboard to be able to easily connect and use the pins.
![Imgur](https://i.imgur.com/eO5Dx5K.png)
![Imgur](https://i.imgur.com/RqIWriu.png)

Speaking of the lights, I used a cheap small strip of WS2811 for this project.  Just about any addressable RGB strip can be used.  Again, cheap and available readily anywhere online (Aliexpress, Amazon, Ebay).  

![Imgur](https://i.imgur.com/Sr3x6n6.png)

As you can see from the image above, the board is powered via a standard micro USB cable and an old cell phone charger power adaptor.  I bought an extra long USB cable and ran it behind the mirror to a plug next to the dresser.

To wire the lights to the board I used some Dupont cables that came with the breadboards (male to male) to the end of the LED strip connector.  The male to male wires fit into the female end of the plug.  Sure I could have done a more legit connection, but it works and I'm lazy.
The led strip is marked with +5V, DO, and GND.  +5V is, well, +5 Volts, GND is the Ground, DO is the data line.  The +5V is plugged into the VIN pin on the NodeMCU, GND to GND, and I put the DO (data) on D4.  You can put it on any pin you choose (D1-D6), you will just need to tell Esphome which pin you used.  More on that in a minute

It's also worth noting (not so much for this project, but for addressable LEDs in general) that the strips do have a "direction".  There is an arrow that is showing the direction that the data travels.  The first LED that is sent data is #1, the down the line up to however many LEDs are on the strip.  You need to know the total number of LEDs you have, again, more on that shortly.

![Imgur](https://i.imgur.com/E3cg1qk.png)

I think that covers all of the hardware.  On to the nitty gritty.  Esphome

Esphome is an AWESOME piece of software that the creator has done a great job of integrating it into HA via a Hassio add-on.  It has tons of stuff built in to handle sensors, lights, etc.  Before Esphome a project like this would require a lot more tinkering and wouldn't work so well with HA.  You would have to find a complicated Arduino sketch / code and hack it together so that it worked on your specific platform then try to get it to talk to HA via MQTT or something else.  Esphome just works, out of the box, without fuss.

Install the Esphome Hassio add on and fire it up then open the WebUI.

![Imgur](https://i.imgur.com/aCKSKNI.png)

This is what should greet you after you login (using your HA user/pass).  

![Imgur](https://i.imgur.com/ymXJhNL.png)

Clicking the dropdown box in the top right corner will show the available ways to write to the boards.  If your board is plugged in you should see /dev/TTYUSB USB UART Controller or similar.
If you don't see /dev/TTYUSB then you should stop and get that going.  That is required for the initial flashing of the NodeMCU.  Once flashed you can do it OTA (Over The Air) via WIFI, but the initial flash has to be done via a USB cable.  In the Esphome documentation there is a way to download a flashtool so you can do it off any computer, it doesn't have to go off the HA box itself but thats how I did it.  Try restarting the add-on after you have plugged the board in, that has worked for me in the past.

![Imgur](https://i.imgur.com/TtjDOoG.png)

Click the "+" circle to add a board.  Give it a name, no spaces, must be lowercase.  Pick what you have from the dropdown (NodeMCU, generic 8266, D1 Mini, etc).  Enter your Wifi info for OTA updates, or dont, I don't care.  Click submit and it will show up on the main screen.

![Imgur](https://i.imgur.com/bbePJoQ.png)

Now we need to tell Esphome what the heck we're going to do with this board.  Click Edit under the new board you just made and you will see a code editor.  In here is where you put the code to do what you want.  You can look through the docs on Esphome to see more examples, I'm just focusing on the LED strip here.


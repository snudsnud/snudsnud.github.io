---
title: Touching Wires (Oh... What a joy it is to be held!)
subtitle: ''
description: Artwork Manual
featured_image: /images/mybrainfeelsfull.jpg
---

Hello! Thank you for taking care of Touching Wires while she is on loan at SGM. This page has an overview of the technical components of the work and some troubleshooting guides. 

If all fails, feel free to get in touch with me! Bern will have my contact details. I have scattered links throughout this page to external sources if you're interested or if I suddenly vanish and you have the time to try and debug it yourself. 

- [System Overview](#system-overview)
- [Bela](#bela)
- [IMPSY](#impsy)
- [Daily Checklist](#daily-checklist)

<br>

# System Overview

![system](/images/touchingwires/system.png)

Touching Wires is made up of a network of two controllers --- the Bela (controls the audiovisual output, and the capacitive sensors), and a Raspberry Pi 4B (runs an Interactive Musical Prediction System [IMPSY](https://smcclab.github.io/nime-embedded-ai/rpi/)). 

Therefore: 
- If there is something wrong with the sound, visuals, or sensors (soft sculptures) the issue is probably with the Bela. 
- If things are running and you're not receiving audiovisual output when nobody is interacting, something's wrong with IMPSY. 

The entire system is connected on it's own network via a TP Link router that we've configured all via ethernet. The passwords for this router are kept on the underside of the router. 

<br>

# Bela

The [Bela](https://learn.bela.io/) is made for sound installations, and I have used a [Trill Craft](https://learn.bela.io/products/trill/about-trill/#trill-craft) to control a series of 'copper tape on vinyl taped to wire' sensors within each soft sculpture. Each is connected to a button clasp that can be connected and disconnected from the Trill breakout board. The breakout board and sensors are the only parts of the circuit that are on the front (public) face of the wall. All other electronics are behind the wall (in the secret room). 

You can access the [Bela IDE](https://learn.bela.io/the-ide/building-a-project/) by visiting `bela.local`. Your usual first point of call is to press the 'build & run' button on the left hand side below the code window. The console underneath should then log a series of booting up messages. 

Things to look out for: 
- **Bela blue lights are not flashing in a heartbeat**: this means the Bela is dead :(
    - Try powering the Bela off and on again, and double check that the SD card is securely in. 
- **Bela IDE not loading**: this means the Bela is not on the network, or not loading the IDE. 
    - Check to see if the Bela is on the network. Easiest way remotely is to try ssh into 'root@bela.local'.
        - If this does not work check the ethernet cables/router. Usual network fixes. 
        - If this does work, she might need a hot minute to load. I'd refresh a few times and after waiting for a bit too long, turn her off and on again. 
- **Trill Craft not found**: this means something is wrong with the craft sensor. 
    - 'Stop' then 'Build & Run' to give it another go to establish the I2C connection. 
    - Check the connector coming out of the hole in the wall and make sure its connected up. The brown jumper cable is GND on the trill, and should connect with the blue jumper cable on the Bela. The series of 4 jumper cables should then be connected in the order they are joined in. 
    - Check that there's no shorting across the circuit on the embroidery hoop. I've used a conductive thread that shouldn't fray too badly but just in case. 
- **Bad host?**: this means it's having troubles talking to IMPSY
    - Check on IMPSY. 

That's all the issues I've encountered before but if there's a secret new error unlocked, please let me know!

<br>

# IMPSY

IMPSY is an open source [Interactive Musical Prediction SYstem](https://github.com/cpmpercussion/impsy). She is made by my supervisor [Dr. Charles Martin](https://charlesmartin.au/). I didn't experience any issues with her until last week when she (I think) overheated and corrupted the SD card with her soul in it. To fix this, we have decided to power down the installation at the end of everyday. 

I will provide the SGM team with a spare copy of IMPSY on an SD card, but it will need some setting up if a swap is warranted. I can also ship in a new IMPSYpi (computer + soul card) if absolutely necessary.  

You can access IMPSY via:

```
ssh pi@impsypi.local
```

The password is: `raspberry`


from here:
```
cd impsy
```

To check on her run:
```
sudo systemctl status impsy-run.service
```

If she's spitting back arrays of values between 0-1, it means she's online and working.

If not, you may need to initiate a restart with:
```
sudo systemctl restart impsy-run.service
```

This may take a bit of time. 

If that doesn't work try:

```
poetry run ./start_impsy.py run
```

Which will initiate the interaction server. And then try restart the service again. 

## If we need to change to a new SD card

If we need to restart IMPSY on a fresh image, i.e use the spare SD. We'll need to do some configuring. 

All code and models can be found on this github repo ([link](https://github.com/snudsnud/TouchingWires/)). Let me know and I can grant you access. 

> Connecting to the Raspberry Pi should be straightforward: if your computer knows about it over USB-ethernet, you can just type http://impsypi.local:4000 into a web browser and see the web UI. The pi gives itself the IP address 169.254.1.107 so you might try http://169.254.1.107:4000 as well if the above doesn’t work.

This web UI allows you to load models and configs in. After doing so, follow the:
```
poetry run ./start_impsy.py run
```
```
sudo systemctl restart impsy-run.service
```

and you should be good to go. 


<br>

# Daily Checklist

This is the daily procedure for powering the installation on/off.

## Start of day 
- Power all devices on.
- Check for Bela heartbeat (flashing blue light)
- Check for IMPSYpi heartbeat (flashing green light)
- Access GUI on projection computer at `bela.local/gui`
- If something's acting up, I'd try refresh the Bela first. 
    - Access `bela.local`
    - Press 'Build & Run'

## End of day
- Power at least Bela and IMPSYpi off. 
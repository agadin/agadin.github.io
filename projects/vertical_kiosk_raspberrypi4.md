---
layout: project
type: project
image: img/cotton/cotton-square.png
title: "Vertical Home Assistant Kiosk- Raspberry Pi4"
date: 9-7-2024
published: true
labels:
  - Home Assistant
  - Raspberry Pi4
summary: "Using a Raspberry Pi4 to display a Home Assitant dashboard on a Vertical TV"
---

<img class="img-fluid" src="../img/cotton/cotton-header.png">

# Overview
For the longest time I wanted to use an old 1080p TV to display a Home Assistant dashboard, so that I did not have to open the home assistant app or go to a website. This project walks through the steps (and many lessons I have learned) trying to implement a fun and simple TV dashboard (famous last words).

# Bill of Materials
* Raspberry Pi 4
* Micro SD card OR Generic SSD ([Excellent Instructions on how to use an SSD HERE](https://thepihut.com/blogs/raspberry-pi-tutorials/how-to-set-up-an-ssd-with-the-raspberry-pi?srsltid=AfmBOopaP_r0aeaVayyqt061wus4h60CtYlRmz_TRV9JxgsqAKnocgtz)- NOTE you will need a SATA to USB adapater!)
* Raspberry Pi USB-C power supply
* HDMI to MicroHDMI 1 meter cable
* UDB mouse and keyboard
* Small Peice of aluminium foil (4cm by 4cm)

> [!NOTE]
> This guide assumes that you already have a working Home Assistant instance and working dashboard asseiable from other devices via a url.

# Instructions

## Preparing the Dashboard
Before a dashboard could be displayed on my ancient TV, I first had to make one in [Home Assistant](https://github.com/home-assistant). In my local home assistant instance I went to `Settings`-> `Dashboards` -> `+ ADD DASHBOARD` button located in the bottom left. After naming it something creative (Kiosk), I clicked open and began to add card entites until I was happy. See below for my implementation and some layout considerations.

## Configuring the Raspberry Pi4 to display a webpage on boot
Getting the raspberry Pi4 (RPI4) to boot into a webpage on boot proved difficult due to the fact that my vertical monitor meant that chromium (the browser) would only start correctly until after the raspberry Pi had completed its full boot cycle and the desktop appeared. This meant that classical method like modifying `/etc/rc.local` ([Described Here](https://raspberrypi-guide.github.io/programming/run-script-on-boot)) or modifying `/etc/xdg/lxsession/LXDE-pi/autostart` ([Described Here](https://www.rickmakes.com/auto-run-chromium-full-screen-on-raspberry-pi-os/)) did not work as those ran the commands mid boot sequence. Essentially chromium did not like the vertical screen orientation and even when started after the boot sequence was complete, it still threw errors in the terminal. Also, in my testing starting chromium during boot occuring before the WIFI connected, so the webpage did not even display at all (Maybe using ethernet could have avoided this). Anyways, that is the context on why I took the approach below:

1. Install `Raspberry Pi OS (64-bit)` onto a SD card/SSD using Raspberry Pi imager on another computer ([Instructions and download details HERE](https://www.raspberrypi.com/software/). This is the full OS with a desktop because I had trouble rotating the screen to be vertical in headlesss only. Also using Raspberry Pi Imager allows you to edit the WIFI credentials before you install the OS which is very nice!
2. Insert the micro SD card or SSD (using a SSD to USB adapater) into the RPI4 after the software has been installed
3. Plug in the the HDMI cable, USB mouse, USB keyboard, and power supply and boot up the RPI4
4. Once logged into the desktop, go to the top left corner and hit the raspberry pi logo (or hit the windows key on your keyboard if it has one). A vertical drop down should appear starting the with `Programing` option, scroll down and click on `Prefrences` and then click `Screen Configeration` from the drop down that appears to the right.
5. In `Screen Configeration`, right-click on the screen and click `Orientation` in the dialoague box that appears. Then select `Left` oritation to roatet the screen 90° to the left.
6. To set up the chromium browser, open chromium by going to the top left raspberry pi logo again, and clicking on `Internet` and selecting `Chromium Web Browser`. In chromium, go to the three dots in the top right and clicking on `settings` option from the drop down. In the settings page search up `Start up settings` and then add the url of the startup sites. I found this to be the easiest way to make sure chromium goes to the dashboard immediently and make the chromium command as simple as possible.
7. Now, it is time to set up the autostart program. The only method I found reliably to work was using a program called `AutoStar` which can be found [HERE](https://github.com/Botspot/autostar) on GitHub (Special thanks to [1500 WATTS AND A WIRE's YOUTUBE VIDEO](https://www.youtube.com/watch?v=4UKJTk5ZrrE) for being a clear solution). Install it by following the instructions on the GitHub.
8. In `AutoStar` (which should have already opened up, but if it is not open then go to the raspberry pi image in the top left corner of the screen, click on `Preferences` and you should see `AutoStar` listed there), click the `+New` button on the bottom right of the window. In the new window enter `chromium-browser.desktop` into the filename, `Chromium Web Browser` into the Display name, and `bash -c "sleep 7; chromium-browser --kiosk"` in the `Command to run`.
9. Click `Ok` in bottom left to save and that is it! Simply reboot your raspberry pi and chromium should automatically start and navigate to your dashboard.
10. To turn the kiosk on and off (to save power please see the `Controlling Kiosk Power` section below)


### Command to run break down
Let's break down the command used to automatically start chromium:

`bash -c "sleep 7; chromium-browser --kiosk"`

1. `bash -c ""`- Designates that we are going to do two bash commands back to back
2. `sleep 7`- freezes the bashscript for 7 seconds to allow for the WIFI to connect (so the webpage will actually load instead of the dinosour game)
3. `chromium-browser --kiosk`- Starts the chromium browser in kiosk mode (which locks down the inputs somewhat, but can be escaped by pressing `ALT`+`F4` at the same time. If you did not modify chromium's `Start up settings` in step 6 above to automatically go to your Home assistant dashboard, then you can put the full URL of your dashboard after `kiosk` in this command (with a space e.g. `chromium-browser --kiosk http://[Homeassistant_IP]:8123/dashboard-kiosk/0` replacing `[Homeassistant_IP]` with the IP of your Home Assistant instance.

## Controlling Kiosk Power
To control the TV/monitor, I used a simple smart plug that Homeassiatnt could control. I personally like shelly-plug-us as they are super snappy and reliable. 

> [!TIP]
> If your TV does not automatically turn on when supplied with power and an active input, you need to depress the power button somehow. See Below!

For a capacitive button, one that you just touch with your finger and tehre is no physical movement, I found that a simple small square of aluminium foil folded a could of times taped to the place where you touch to power on the TV works (make sure the the aluminium foil is touching the surface of the capacitive button and not insulated by the tape!). If that is not working try wraping an exposed male end of a dupont cable in the aluminium foil first and then taping the Aluminium foil +dupont to the surface of the sensor. If this does not work/causes weird behavior you can probably use an ESP32 to ground the capcitive sensor on command. A theorectical setup would involve an ESP32 (e.g. D1 mini) running [ESPHOME](https://esphome.io/index.html) and connective the other end (the one not taped to the power switch) of the dupoint foil contraption made earlier to one of the GPIO pins (e.g. D3). Then using a GPIO switch and adding the following code may work:
```
switch:
  - platform: gpio
    pin: GPIOXX #replace with your corresponding GPIO pin HERE
    id: relay
    on_turn_on:
    - delay: 500ms
    - switch.turn_off: relay
```
No clue if this actually will works (I have not tested it), so your milage may very!

If you have a physical button, I would use a switch bot to depress the button. 


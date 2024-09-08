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

## Preparing the Dashboard
Before a dashboard could be displayed on my ancient TV, I first had to make one in [Home Assistant](https://github.com/home-assistant). In my local home assistant instance I went to `Settings`-> `Dashboards` -> `+ ADD DASHBOARD` button located in the bottom left. After naming it something creative (Kiosk), I clicked open and began to add card entites until I was happy. See below for my implementation and some layout considerations.


<hr>

<pre>
You open your eyes, and you are greeted by an unfamiliar ceiling.
Startled, you get to your feet and quickly scan your surroundings. It's
dark except for the stream of light coming from a crack on the only boarded
window in the room. You try to peek through the crack, but you cannot see
anything. You wonder where you are and who could have possibly brought you here.

<--------------------help------------------------>
Enter quit or one of the following commands -
Weld light look walk pickup inventory help h ?
<------------------------------------------------>

look
The room is a picture of decay with only a faded number identifying it as room-4. The bed you were
 lying on is stained with what looks like dried blood. Could it be your blood? No - it is not. The
 only way out of the room aside from the door to the corridor is a window that is boarded shut. It
 looks like it has been like that for decades. There is a door going west from here. You see a candle
 on the floor. You see a match on the floor.

pickup candle
- you are now carrying the candle -

pickup match
- you are now carrying the match -

light match candle

The candle is now lit. It illuminates everything in the room.

walk west
The corridor is lit with the candle. It is so long that you cannot see to the end. You notice that
 there are words written on the wall. There is a door going east from here. There is a way going north
 from here. There is a door going south from here.
</pre>

<hr>

Source: <a href="https://github.com/jogarces/ics-313-text-game"><i class="large github icon "></i>jogarces/ics-313-text-game</a>

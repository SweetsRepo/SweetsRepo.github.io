---
layout: post
title: Hoonicarus - The car that flew too close to the sun
date: 2017-06-18T17:16:00.000Z
categories: update
---

# The IDE Cup 

For an in class project students were tasked with the creation of an autonomous
model car that was capable of running around an arbitrary track at maximum 
speed. To those familiar with the Freescale/NXP cup, this is the continuation 
of such competition. In pursuit of the podium my partner and I encounted many
issues and ultimately were unable to perform on competition day. This post will 
discuss some of the issues we experienced and show the car when it was in working
order. Additionally I will discuss some of the approaches we used to achieve 
higher velocities. 

## The Sub Issues

Our first motor control shield suffered from a burnt resistor for the servo 
power connection. This created an immideate setback as we had to get a new pin
header and solder it onto the board. 

The next issue we had was over-braking. The car had no break system, therefore
breaking was achieved by reverse driving the motors proportional to the amount
of time spent traveling in a straight line. However, tuning this system was largely
heuristic and could cause full reversal in a set of subsequent corners. This was 
later corrected by adding in a counter to keep track of the time spent in 
successive corners (assuming no acceleration while turning).

The last issue came in the form of the destruction of our DC Motors. Excessive
testing of the breaking and drifting code caused high motor temperature which 
made later tests maximum speeds significantly slower. It is proposed that the 
excessive heating melted the enamel coatings around the motor coils, a known
phenomena for high temperature operation.

## The Main Issue

Overall our car proved unable to perform well on competition day due to high
frequency noise within the entire system. Due to changing of the motors, motor 
control shield, and testing with another microcontroller it is supposed that backfed
current to one of the io pins of the microcontroller caused permanent damage to
the system. This random noise then propogated to the camera data causing unpredictable
behavior in the path finding algorithm. 

## Innovation and Practice Run

Despite the failure to perform at competition, a few good runs of our car were
captured. Below is a link to the car during practice, where braking, drifting,
and adaptive speed control may be seen:

[Practice Run](https://youtu.be/5yPk6YW4bvY)

In future years we would like to experiment with new micrcontrollers and new 
hardware for the car itself.


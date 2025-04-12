---
title: 3D Printing Rennaisance
date: '2021-02-01T12:31:00-08:00'
tags: life
---
![3d printer enclosure](/assets/enclosure.jpg)

I learned halfway through January how easy it was to capsize the catamaran when I failed to shift my weight fast enough during a tacking maneuver in a 30 knot wind.  So I've gotten back into 3d printing in a big way in anticipation of needing to print water-tight enclosures for the electronics in my sailboat anemometer project.  After dusting off my Creality Ender 3 printer I was reminded of the challenges of open-air ABS printing, specifically, preventing uneven cooling that leads to warped ABS prints. To combat this, I built an Ikea Lack enclosure to better trap heat and maintain temperature consistency. 

 Why do I print in ABS if it's such a difficult material you ask?  ABS has a lot of properties to recommend it in a nautical environment. These include: It's much more durable than PLA, unlike PETG it's largely unaffected by moisture, and it lends itself more to sanding and finishing.  You can even apply [an acetone polish](https://all3dp.com/2/abs-smoothing-a-beginners-guide-to-abs-vapor-smoothing/) to ensure the impermeability of the finished product.

![Printrbot Simple](/assets/printrbot.jpg)

Needless to say 3d printing has come a long way since I put together my first Printrbot Simple kit in 2014.  My original kit, pictured as fully assembled on the right, used a balsawood construction.  The extruder tip was fixed and only supported PLA. It had a print volume of 100mm cubed, did not support a heated bed, and cost $400.  In comparison the Ender 3 today [costs half as much](https://all3dp.com/1/creality-ender-3-3d-printer-review/), includes a heated bed and replaceable extruder tip, and boasts an all-metal construction. It has more than double the print volume, and can print in [PLA, ABS, PETG, TPU, HIPS, and PVA](https://all3dp.com/2/ender-3-filament-guide-materials-you-can-3d-print/).  With modifications it can also print in metalfill, wood, and glow-in-the-dark filament. I've had a blast using it, and I even re-installed [Blender](https://www.blender.org/) so that I could generate my own designs.  

3D printing hasn't been the only thing I've been working on though.  I've started a multi-platform BLE FTMS library for Android and iOS.  I've also created a [C-based FTMS server](https://github.com/jamesjmtaylor/esp32-ftms-server) for my ESP32.  I'm not very far yet, but it looks like it should be pretty straightforward to develop the features necessary to fulfill my new-year's resolutions.  But more on that next month.

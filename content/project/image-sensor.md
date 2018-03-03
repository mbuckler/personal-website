+++
# Date this page was created.
date = "2017-04-03"

# Project title.
title = "Configurable Imaging Sensor"

# Project summary to display on homepage.
summary = "Tapeout of a configurable and energy-proportional image sensor"

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "sensor_control_gds.png"

# Tags: can be used for filtering projects.
tags = ["imaging"]

# Optional external URL for project (replaces project detail page).
external_link = ""

# Does the project detail page use math formatting?
math = false

# Optional featured image (relative to `static/img/` folder).
[header]
image = ""
caption = ""

+++


While most image sensors can be configured to output a subset of their total
resolution, this doesn't usually lead to energy savings as might have been
expected [\[LiKamWa-2013\]](http://dl.acm.org/citation.cfm?id=2464448). LiKamWa
et al. explored energy saving methods possible when using existing sensors, but
our project took the idea further by making a new energy-proportional image
sensor. [Suren Jayasuriya](http://www.andrew.cmu.edu/user/sjayasur/website.html)
and I did this by adding power gating transistors to the amplifier and ADC in
each of the sensor's readout columns.

As the digital design lead on the project I wrote Verilog RTL to describe a
state machine based controller for the rolling-shutter readout and control of
the column power gating. I pushed my design through Synopsys synthesis and Suren
connected the resulting digital layout to his pixels and other analog circuitry.
We taped out this design in IBM's 130nm technology.


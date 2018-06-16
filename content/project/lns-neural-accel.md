+++
# Date this page was created.
date = "2017-04-04"

# Project title.
title = "Neural Network Accelerator with Logarithmic Number System"

# Project summary to display on homepage.
summary = "Hardware accelerator for neural network computation using the LNS"

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "LNS_Accel.PNG"

# Tags: can be used for filtering projects.
tags = ["machine-learning","computer-architecture","neural-networks"]

# Optional external URL for project (replaces project detail page).
#external_link = "https://github.com/cucapra/log-neural-accel"

# Does the project detail page use math formatting?
math = false

+++

For my final project in Cornell's [Complex ASIC Design
course](https://web.csl.cornell.edu/courses/ece5745/) I built a neural network
accelerator which used the [Logarithmic Number System
(LNS)](https://en.wikipedia.org/wiki/Logarithmic_number_system). There has been
some interest in [using the LNS with neural
networks](https://arxiv.org/pdf/1603.01025.pdf) as neural computation is
multiplication heavy and multiplications in the LNS can be implemented with a
simple adder. I augmented a
[PyMTL](http://www.csl.cornell.edu/~cbatten/pdfs/lockhart-pymtl-micro2014.pdf)
implementation of a single threaded
[PARCv2](https://web.csl.cornell.edu/courses/ece4750/handouts/ece4750-parc-isa.pdf)
processor with instructions to convert between LNS and fixed point, add LNS
numbers, and multiply LNS numbers. I also built a matrix multiplication
accelerator which could either use fixed point or the LNS. After integrating the
accelerator with the processor and evaluating the various possible designs, it
became clear that the delay and energy benefits gained by using the accelerator
(generic cpu vs custom data and control path) far outweighed any savings in the
ALU (LNS vs fixed point). This is due to two properties of matrix
multiplication: 1) it has a regular dataflow that custom hardware can exploit
well, and 2) benefits from cheap LNS multiplications wash out when the expensive
but necessary LNS additions are used as well.

[The code](https://github.com/cucapra/log-neural-accel) for this project is
currently set to private due to the fact that I can't release details about my
baseline processor. Depending on your needs I may still be able to help you
though, so contact me if you're interested.

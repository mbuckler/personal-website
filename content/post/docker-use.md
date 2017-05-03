+++
date = "2017-04-25T16:25:31-04:00"
highlight = true
math = false
tags = []
title = "Developing in Docker"

[header]
  caption = ""
  image = ""

+++

Most of us academic/industrial ECE/CS researchers want to make our
results as reproducible as possible. Its good for the integrity of our
field and it also helps future researchers and developers to build on
our prior work. [GitHub](https://github.com/) is great for distributing
software, but code can rarely be compiled and run on its own as nearly
all modern software relies heavily on packages and dependencies.
Installing these dependencies can be incredibly frustrating or possibly
even impossible depending on how long ago the code was written. 
Thankfully, [Docker](https://www.docker.com/) is here to save the day!

What benefits does Docker provide? Running code or even product
deployment without ever installing dependencies, enabling multiple
dependency versions on a server without conflicts, "pulling" of
environments from [Docker Hub](https://hub.docker.com/) as simply and
easily as cloning a GitHub repository, and all without noticeable
performance degradation.

So, how does Docker provide all of this modularity and portability? By
very nearly being a Virtual Machine (VM) but not going the full way. [See
here for more
details](https://docs.docker.com/get-started/#containers-vs-virtual-machines),
but rather than implementing an entire OS as a VM might do, an
instantiation of a Docker image (called a "Container") only implements
binaries, libraries, and applications. This makes running a Windows
image on Linux or vice versa tricky since they don't have the same
kernel, but since most of the folks I know develop on Linux this hasn't 
been a problem for me. In case you're interested in running Docker on
macOS or Windows check out [this blog
post](http://containerjournal.com/2016/08/15/docker-not-just-linux-anymore/).

#![Docker at a high level](/img/docker_high_level.png)

Test 

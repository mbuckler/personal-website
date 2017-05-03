+++
date = "2017-04-01T16:25:31-04:00"
highlight = true
math = false
tags = []
title = "How to Install NVIDIA GPU Drivers & CUDA"

[header]
  caption = ""
  image = ""

+++

Anyone who's set up a new Linux based, GPU enabled, deep learning system 
knows the horror that is driver installation. While it is technically
possible to install NVIDIA drivers and CUDA from your package manager,
the most up to date versions aren't available and in the worst case you
might even break graphics on your machine. After a great deal of
difficulties installing and reinstalling, I finally found a viable
strategy: installing both CUDA and proprietary Linux drivers from an
NVIDA run file without OpenGL libraries.

The installation instructions shown below are largely taken from a two
year old 
[forum
post](https://devtalk.nvidia.com/default/topic/878117/-solved-titan-x-for-cuda-7-5-login-loop-error-ubuntu-14-04-/) 
on NVIDIA's developer forum with a question asked by a user called
NeuroSurfer and answered by txbob. I think it is worth reposting the 
instructions here because I've added a few things, and also because 
I think a formal post will help bring more exposure to this solution. 

Without further ado, here is how to install NVIDIA drivers and CUDA on
your Linux machine. I have tested these instructions on systems running
Ubuntu 14.04 and 16.04 and with Titan X and GTX 980 cards.

1. Preferably start with a fresh install of your OS, but if you are
hoping to avoid a full OS re-install you first need to purge your
system of any existing NVIDIA drivers installed via the package manager.

	```
	sudo apt-get remove --purge nvidia-*
	```

2. Download the run file for the CUDA version that you want. You can
find the download link
[here](https://developer.nvidia.com/cuda-downloads). Make sure to get
the run file and not the debian installer. Change the permissions of it
so that it can be executed.

	```
	chmod a+x .
	```

3. Verify that build-essential is installed

	```
	sudo apt-get install build-essential
	```

4. Remove your xorg.conf if it exists

	```
	sudo rm /etc/X11/xorg.conf
	```

5. Create a blacklist file for nouveau (default Ubuntu graphics) at
`/etc/modprobe.d/blacklist-nouveau.conf`. Put in the following contents:

	```
	blacklist nouveau
	options nouveau modeset=0
	```

6. Update your kernel's ramdisk

	```
	sudo update-initramfs -u
	```

7. Reboot your machine, nothing should have changed

8. Use `Ctrl-Alt-F1` to drop to a terminal, log in.

9. Stop your display manager. Lightdm is default for Ubuntu

	```
	sudo service lightdm stop
	```

10. Navigate back to where you downloaded your run file. Run it and
explicitly avoid installing OpenGL libraries with the command line
option.

	```
	sudo ./cuda-X.Y.ZZ_linux.run --no-opengl-libs
	```
  * Accept the EULA
  * Install the driver? Yes
  * Update the Xserver config? No
  * Install CUDA? Yes
  * Install the CUDA samples? Yes


11. Install nvidia-modprobe. This avoids that annoying
"cudaGetDeviceCount returned 30 -> unknown error" issue.

	```
	sudo apt-get install nvidia-modprobe
	```

12. Edit your ~/.bashrc to update your path variables

	```
	export PATH=/usr/local/cuda-7.0/bin:$PATH
	export LD_LIBRARY_PATH=/usr/local/cuda-7.0/lib64:$LD_LIBRARY_PATH
	```

13. Update your paths

	```
	source ~/.bashrc
	```

14. Check that the CUDA version is correct.

	```
	nvcc -V
	```

15. Check that your GPU's are visible to the system and have the right
driver versions.

	```
	nvidia-smi
	```

16. Reboot your system, pray to our Lord and Savior Elon Musk. If his
kindly gaze falls upon you, you will have a fully functional CUDA
enabled Linux machine. Enjoy!

17. Troubleshooting and final thoughts

If you experience a login loop (are able to get to the login screen but 
when you try to log in you are kicked back to the login screen) then it
is likely that you forgot to include the `--no-opengl-libs` command when
running the installation runfile.

If after booting you see a black screen with a blinking unresponsive 
cursor, then you likely have something wrong with your display manager.
Re-install lightdm or install and switch to gdm.

Because this installation doesn't go through the package manager you may
experience issues when upgrading your kernel. If you'd like a way to
automatically re-install on an update you may be interested in [this
solution](https://ubuntuforums.org/showthread.php?t=835573) as well.

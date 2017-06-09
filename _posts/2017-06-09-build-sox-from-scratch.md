---
layout: page
title: Building SoX from scratch, aka an excercise in self-fladgellation
---

For people who just want the instructions, go [here](http://akshayc.com/blog/build-sox-from-scratch/#instructions). For people who want to read about my frustration, pain, and the road to my eventual success, keep reading. 

Thanks to Stuart Langridge for his [blogpost](https://kryogenix.org/days/2014/11/18/making-a-static-build-of-sox/) from which, I was able to leverage ideas, and was able to understand the syntax for the configure that I finally used.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Introduction](#introduction)
- [The setup (of the story)](#the-setup-of-the-story)
- [Storm Clouds in the Horizon](#storm-clouds-in-the-horizon)
- [The Actual Meat of the matter:](#the-actual-meat-of-the-matter)
  - [Requirements](#requirements)
  - [Instructions](#instructions)
    - [Installing the dependencies](#installing-the-dependencies)
    - [Now the beast SoX](#now-the-beast-sox)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Table of Contents
=================

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)


## Introduction
[SoX](http://sox.sourceforge.net/) is a self-proclaimed swiss army knife of sound processing programs. Indeed, I have used it quite a lot in my years as a speech recognition researcher. It gives you really quick and quite intuitive, and frankly really powerful options to modify audio files right from the command line.

Usually, one simply uses a package manager available for your linux distributions (all hail the all powerful apt), to simply pull in a precomiled binary, and you are set to go. However, when you are like me, and have to work in scenarios where you are not, and honestly, dont want to be root (waaay too much responsibility), you have to suck it up and start installing stuff from scratch AKA compile from source.

This is a story of trials, sufferings, pains, plot-twists, drama, oh what the hell, let's just get to it...

## The setup (of the story)

Picture this, a wide eyed, bushy tailed... who am I kidding? sleep-deprived, highly caffeinated man-child sits down at his desk, preparing to get a jumpstart on his work. As a part of my work, my advisor and I have decided that we should run the GALE Mandarin speech recognition baseline from Kaldi for, you guessed it, getting some baselines. The caveat: We needed to run this in a SLURM cluster in the CMU Pittsburgh campus. Gone are the days when I could just trundle these jobs away in my humble local 4 GeForce TitanX powered 12 core 128GB RAM (#brag) machine. I am in the big leagues now, jostling with other equally motivated, possibly even more caffienated, researchers in multiple domains, vying for some scraps of compute time in a relatively new, but already overbooked HPC cluster.

Those in the know will be aware of the fact that in these SLURM configurations, you are usually a pleb, without sudo access, and usually reliant on the good graces of a lord (sysadmin: the one who maintains the cluster) to help you install modules to the cluster so that you can call them and run stuff. However, our sysadmin is a person who does this tiny thing called a PhD on the side. From reading and viewing the PhDcomics, you (should be/will become) aware of the fact that us PhD candidates are usually high strung, trying to balance a stringent research schedule, amazing social life, deadline-filled weeks, all while writing research papers (one of the above is a patent lie). This person has my never-ending respect because despite all this, he also agreed to become the sysadmin for this cluster.


Given the above, and the fact that each research group has its own set of idisyncratic toolkits that they prefer to use for various projects, it falls to a person within the group to figure out how to install the software to get your job done.

## Storm Clouds in the Horizon

So, where were we? Oh yes, GALE Mandarin! This particular dataset has it's own idiosyncracies as well. Even though it is maintined by the LDC, for whatever reason, the data was stored in FLAC format. Shut up you audiophiles. I know FLAC is lossless, high quality, better lossless compression, yada yada... The point I am trying to make is, they could have simply stored it in wavfiles for easy parsing by existing tools, but no.. So the upshot is that we now have a dependency on a third party tool, in this case SoX to be able to read this data, and split it into chunks based on timestamps from ground-truth transcriptions.

SoX was last updated, as of writing this, on 22nd February 2015, which in distribution terms, could be considered as the universe having undergone atleast 3 big-bang-to-big-crunchs. Other programmers can, and will relate: Dependencies are broken. Instructions are outdated, documentation for compilation is non-existent/incomrehensible/outdated (it's a spectrum you see...), yada yada, yada...  So all-in-all, I knew I had to stiffen up my upper lip, gird my loins, and get down and dirty with the intricacies of this process.

to be continued...

## The Actual Meat of the matter:

Ok \</rant/>. The following are the steps I took to get the compilation done:
As I said at the start, thanks to Stuart Langridge for his [blogpost](https://kryogenix.org/days/2014/11/18/making-a-static-build-of-sox/)

### Requirements

1. We want sox to be compiled from source.
2. We want it to be able to handle formats like mp3, flac, oggvorbis
3. We do not have access to sudo
4. We need to not have access to the dependencies like liblame, libflac, libogg, libvorbis in the usual /usr/local
5. We don't care if the library is compiled with static or shared....

### Instructions

#### Installing the dependencies

Since we want to support handling of MP3, FLAC, OGG-VORBIS file formats, we first need to compile these libraries and store them in a folder we have access to.
In my case, I will be storing everything in /home/akshayc/local

*NOTE*: As a part of my exploration of how to get the tools installed, I had compiled autotools from source because the module available in the cluster apparently did not have libltbl for some reason, which could possibly be a reason the below steps work. If you find that these instructions dont work, and you get errors regarding libltbl, then you may have to resort to this too.

```
# Prerequisites
### LAME:
wget -O lame-3.99.5.tar.gz "http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Flame%2Ffiles%2Flame%2F3.99%2F&ts=1416316457&use_mirror=kent"

# You can go to the sourceforge download page and get a link
# to a better version if available. Hopefully it won't break.
tar -xf lame-3.99.5.tar.gz
cd lame-3.99.5
./configure --prefix=/home/akshayc/local
make clean
make -j`nproc`
make install
cd ..

# Link for from where I got OGG, VORBIS and FLAC:
https://www.xiph.org/downloads/
### OGG:
# right click on the download link and select copy link address, then wget it.. like:
wget http://downloads.xiph.org/releases/ogg/libogg-1.3.2.tar.gz
tar -xf libogg-1.3.2.tar.gz
cd libogg-1.3.2
./configure --prefix=/home/akshayc/local
cd -
# Do the same for VORBIS, and then FLAC
```

#### Now the beast SoX

Now that we have all the dependencies installed to `/home/akshayc/local`, we can get to installing SoX

While SoX apparently can do dynamic linking of the dependencies using libtldl, in the cluster I was working with, I was unable to get that working
However, I found the following to work:

```

# Go to https://sourceforge.net/projects/sox/files/sox/
# Click on the folder of the version you are interested in.
# 14.4.1 is broken in some respects. So, go for 14.4.2.
# You will want to remove the /download at the end of the link

wget https://sourceforge.net/projects/sox/files/sox/14.4.2/sox-14.4.2.tar.gz

tar -xf sox-14.4.2.tar.gz
cd sox-14.4.2
# If configure is not present, (ideally is should be)
autoreconf -ivf
# We need to make sure that the linker can find all the dependencies,
# and that the includes can also be found. Surprisingly, given that,
# the configure is pretty good. 
./configure LDFLAGS=-L/home/akshayc/local/lib \
	    CFLAGS=-I/home/akshayc/local/include \
	    --prefix=/home/akshayc/local

make -s
make install
cd -

```

Ensure that in your bashrc, you have
```
LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/home/akshayc/local/lib:/home/akshayc/local/lib64
PATH=${PATH}:/home/akshayc/local/bin
```

This should be enough, and you should be able to use the sox binary for all your nefarious needs.

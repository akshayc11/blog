---
layout: page
title: Building SoX from scratch, aka an excercise in self-fladgellation
---

For people who just want the instructions, go [here](#). For people who want to read about my frustration, pain, and the road to my eventual success, keep reading. 

##Introduction
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


## 
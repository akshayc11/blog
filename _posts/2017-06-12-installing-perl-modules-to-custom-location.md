---
layout: page
title: Installing Perl Modules to a custom location from source
---

This article describes a way for installing Perl 5 modules in a custom location and using them.

Table of Contents
-----------------

* TOC
{:toc}

## Introduction:
This post is about how to install Perl Modules from source into a custom location and be able to use them using the default @INC within a perl script.

**NOTE**: I am a complete Newb in terms of Perl programming, and there could be much more easier ways of doing what I did here.

## System Constraints:
This need arose due to some perl scripts that needed to be run in a SLURM system where the backend machines did not have access to the internet.

## Procedure:
1. Go to [http://www.cpan.org/](http://www.cpan.org/), and in the search bar, type the name of the package you want.
2. Click on the result you want, and look for a link with the name *Source*. It could also say *raw*, *tar.gz*, *tar.bz2*
3. Copy the link
4. Download the source to your working directory
5. Do `tar -xf <source.tgz/tz2>`
6. Do `cd <untarred directory>`
7. There should be a file called Makefile.PL. Do `perl Makefile.PL PREFIX=<custom-dir> LIB=<customdir>`
8. `make && make install`
9. Modify .bashrc to include the libraries as:
`export PERL5LIB=${PERL5LIB}:<custom-dir>`
Note that this may change depending on how the package gets installed.
10. restart the console, and you should be good to go.

---
layout: page
title: A crash course in Kaldi's chain models
---

I attempt to decipher the intricacies of chain models within the Kaldi toolkit

# A crash course in Kaldi's chain models

*NOTE: This is just my understanding, and is totally guaranteed to contain multiple errors. So take everything I say here with a huge dose of salt.*

Forewarning: Though I have used Kaldi extensively to build GMM acoustic models, I have only scratched the usage of the neural network capabilities within the toolkit.
This post constitutes my efforts to learn the intricacies of chain models within the kaldi framework.



For the official documentation, please go to [http://kaldi-asr.org/doc/dnn3.html](http://kaldi-asr.org/doc/dnn3.html).

## History

Well, at first, there was nnet, developed by Karel Vesely in Kaldi. Then Dan Povey came along with his own implementation which was christened as nnet2. Then, a more modular and flexible version arose,quite quickly, which was christened as nnet3

## A basic Tutorial on how to build an nnet3

First, let's setup the problem 
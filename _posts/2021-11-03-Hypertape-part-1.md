---
layout: post
title: Hypertape, part 1
tags: [PAL-1, KIM-1, audiotape, 6502, historical software]
---

Making a Python script to convert audio tape to binary code for my PAL-1 seemed somewhat useful, but especially instructive as since I wanted to learn more on how that worked. Showing off a working prototype at the PAL-1 forum I got asked: but can it do hypertape. Oops... no. So, how does that hypertape work? Back to the drawing board.

## Hypertape, part 1

'Back in the day', when serial interfacing using a teletype or glass terminal was usually way out of reach for the average [KIM-1](https://en.wikipedia.org/wiki/KIM-1) enthusiast, loading programs was usually done using the audio tape interface. Taperecorders were reasonably priced and many people would already own one for recording and playing speech and music. 

The audio tape format is well described in the [KIM-1 manual](http://retro.hansotten.nl/uploads/files/MCS6500%20Hardware%20Manual%20jan%201976%20second%20edition.pdf). 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/KIM_manual_audio.png?raw=true" alt="fig1" style="width: 400px;"/>

It is actually fairly easy to recreate in code, but that will be the topic for another post. For now I was interested to see if hypertape would work on the PAL-1. I haven't yet implemented hypertape format in my program that converts binary to audio (.wav). So I used the KIMposer website to create some hypertape audio files. But loading on the PAL-1 failed. So, what is going on?

The KIM-1 audio input can be checked by [probing the PLL pin with a scope](http://retro.hansotten.nl/6502-sbc/kim-1-manuals-and-software/kim-1-articles/reading-tapes-from-other-kim-1/). The PAL-1 has a similar pin out (acutally, exactly the same if you study the circuit diagrams, it's just called 'cassette'). 

So, what does the input look like for a 'normal' tape format, and how is that converted by the PAL-1 cassette interface? 

The following is an audiogram created from my own script:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/vutape9.9365_start_waves.png?raw=true" alt="fig1" style="width: 800px;"/>

You can clearly see the difference in frequencies between 2400 and 3700 hz, and the prescribed length of each frequency duration. If we zoom in a bit we can appreciate the individual waves better:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/vutape_myself9.9367_start_waves_short.png?raw=true" alt="fig1" style="width: 800px;"/>

Looking at the figures of the waves they look quite ideal, and they should be as they are generated to be just that. However, the output I captured from the PAL-1 earlier also looks quite good (from the recording, the actual audio output looks even better):

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/out_pal11.5029_start_waves_short.png?raw=true" alt="fig1" style="width: 800px;"/>

If we look at a Fast Fourier Transform (FFT) of the input that I'm going to feed to the PAL-1, the pattern is more or less as expected with peaks at the expected frequencies.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/first_ex_book_of_kim_fft.png?raw=true" alt="fig1" style="width: 800px;"/>

When we feed the audio to the cassette interface of the PAL-1, and probe the cassette pin, the following image comes from the scope:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/mywav_1.png?raw=true" alt="fig1" style="width: 500px;"/>

Zooming in and measuring the wave, we see that my own audio file is slightly off in timing, about 1.7% faster than indicated in the KIM-1 manual, but probably reasonably within spec. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/mywav_2.png?raw=true" alt="fig1" style="width: 500px;"/>

Ok, so what about hypertape? Well this is an [interesting story in itself, detailed](http://retro.hansotten.nl/uploads/files/kb11-hypertape.pdf) by the (co-)inventor of hypertape and 6502 guru, [Jim Butterfield](https://en.wikipedia.org/wiki/Jim_Butterfield). The KIM algorithm for 'listening' to the input doesn't care for absolute timing at all, it cares about the ratio of time of high and low frequency sound. Effectively, the duration of each frequency can be compressed to 1/6th. The secret is to make use of 'half-cycles', which allow a short duration interval of 3700 hz to be compressed to 1.5 pulses. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/hyper_waves.png?raw=true" alt="fig1" style="width: 200px;"/>

Interestingly, Butterfield remarks in the article that the sound is different when you listen to it yourself. And that makes sense looking at the FFT:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/kimposer_hyper_fft.png?raw=true" alt="fig1" style="width: 800px;"/>

The FFT is from a digitally generated audio file with the [KIMposer tool](https://www.retroplace.com/en/kimposer), which can output the normal and hypertape formats.

Looking at the waves in the audio file, it is clear that KIMposer aims to apply the format prescribed by butterfield, although it is not obviously the same.  

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/KIMposer_hyper0.7433_start_waves_short.png?raw=true" alt="fig1" style="width: 800px;"/>

What certainly is clear is that the format uses half-cycles which makes the pattern far less legible to the human eye as we try to interpret the pattern as 0s and 1s. It also explains the FFT - the concentration of frequencies inbetween 2400 and 3700 hz.

Ok, so how what happens when we feed this audio file to the PAL-1 and probe the cassette pin?

It is fairly clear that something is amiss right away. The low and high patterns seem out of proportion and there even appear to be gaps.
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/hyper_KIMposer_1.png?raw=true" alt="fig1" style="width: 500px;"/>

Zooming in, it becomes more clear how the ratio of high/low frequency interval duration isn't quite right. Also, the bits seem to be a bit short, every bit is expected to last a little over 1ms. Some bits are right in timing, but others, like this one, is not. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/hyper_KIMposer_4.png?raw=true" alt="fig1" style="width: 500px;"/>

But probably even more problematic is that some phase transitions result in bouncing of the high signal. This can't be right and certainly is enough to make the hypertape fail.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/hyper_KIMposer_3.png?raw=true" alt="fig1" style="width: 500px;"/>

So, what now? Not sure, this needs to be continued. In the mean time I have amended my program to convert audio to binary with the option to read hypertape. That does work, at least on the KIMposer-generated audio. But feeding that same audio to the PAL-1 does not work. Bummer.

A little postscript: Interestingly, at the [site of Hans Otten](http://retro.hansotten.nl/6502-sbc/kim-1-manuals-and-software/kim-1-manuals-and-software/) a bunch of WAV files can be found from the first book of KIM. These seem generated by the [KIMTAPE software](http://retro.hansotten.nl/other-retro-articles/kim-1-utilities/#kimtape), although I'm not sure. These audio files look rather different - not only are they not square-waved, they seem to be close to half-duration compared to the formal specifications - so, a bit like a 'slow hypertape'. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/asteroid7.9645_start_waves_short.png?raw=true" alt="fig1" style="width: 800px;"/>

The PAL-1 seems to read these fine, but the duration of intervals is about half, as expected. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/hypertape/tape_from_HO_FBoK_2.png?raw=true" alt="fig1" style="width: 500px;"/>

This hypertape story will be continued, for sure. In the mean time, if you're interested, check out some code I've made [to convert and analyze audio tapes for KIM-1](https://github.com/hjmegens/PAL1_tools). Note though, this project is still very much a work in progress. 

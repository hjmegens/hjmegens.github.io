---
layout: post
title: Micro-Professor MPF-1B
tags: [Micro-Professor, MPF-1B, Z80, SBC, ROM]
---


I recently got an interesting Z80 computer/trainer Single Board Computer: the Multitech Micro-Professor MPF-1B. It is an attractive little board that comes with a case that looks like a book. Here I document some of my first experiences with the little board, which, unfortunately, isn't too happy. 

## Micro-Professor MPF-1B


I have a great fondness for the late-'70s and early '80s 8-bit-based Single Board Computers. There are a few reasons for that. One is that the minimalistic architecture allows you to explore how the computer functions in every detail. While I also appreciate modern designs that follow the same principles, such as the [RC2014](https://z80kits.com), there is a certain element to the vintage architecture that isn't quite captured by these "modern" 8-bit computers. 

And so, while I really love my RC2014 to explore the [Z80](https://en.wikipedia.org/wiki/Zilog_Z80) world, when I had the opportunity to buy a [Micro-Professor](https://en.wikipedia.org/wiki/Micro-Professor_MPF-I), or MPF-1, for a reasonable price, I jumped on it. If anything, the marketing of this cute SBC is unlike anything I've ever seen: a book-like cover that fits on your bookshelf. From the picture featuring on the box and manual it is clear that marketing was probably more oriented towards parents of kids, or other adults. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20211108_195213_.jpg?raw=true" alt="fig1" style="width: 500px;"/>

Apart from the interesting packaging, it was also the [monitor program](https://en.wikipedia.org/wiki/Machine_code_monitor) that intrigued me. Monitors found in SBC such as the [KIM-1](https://en.wikipedia.org/wiki/KIM-1), [SDK-85](https://en.wikipedia.org/wiki/Intel_System_Development_Kit) and MPF are cleverly designed programs that gave a limited but essential set of functions to interact with the device. These functions included driving the LED display, scanning the minimal keyboard, often not much more than a hexpad, and usually at least one additional means of input, e.g. by audio transfer via the household taperecorder. Importantly, everything the monitor does revolves around addresses and machine code. You really have to learn the essentials of the computer to be able to work with it. These SBCs, sometimes called 'trainers', usually came with [excellent manuals](https://electrickery.hosting.philpem.me.uk/comp/mpf1/doc/MPF-1_usersManual.pdf) that allowed the novice to explore the 8-bit computer and the interfacing options in considerable depth. 

The MPF was sent to me by the seller, and when I opened the box there was a very distinct air of mold coming out. Fortunately, the books that came with the MPF - a complete set of manuals and some other interesting booklets - turned out not to be affected by water damage. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210725_163336_.jpg?raw=true" alt="fig1" style="width: 500px;"/>

The same could not be said for the original box that came with it. That did not bode well for the MPF itself. Fortunately, the book-shaped cover and SBC both seemed in a good state and no obvious visual water damage was apparent.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210717_124718_.jpg?raw=true" alt="fig1" style="width: 400px;"/>


The board reeked of mold though. Inspecting the board a bit closer it was clear that there was tons of corrosion on the pins of some chips, although others seemed fine. I guess it just depends on the manufacturer or type of chip what metal was used for the pins. But it was clear that this device had seen at least one period of very damp storage.  

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210717_124732_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

Not surprisingly, perhaps, the MPF-1 proved dead when I tried it - no sign of life at all. The reason could lie with any one of the chips and most of the capacitors. The previous owner did do some serious hacking with the board: the experimentation area has a few components that are connected by wire at the back of the board. There were also other wires rerouting leads but it wasn't obvious to me what it all was supposed to do. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210717_124639_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

There was a ROM in the normally empty socket that can hold either additional RAM or a custom ROM with your own program. Clearly, the previous owner had been doing some developmental work but what that entailed I couldn't figure out.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210717_132355_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

I decided to remove all the custom wiring and ROM hoping that the board was in fact functioning, but that something of the custom stuff was simply tying up the display somehow. Fortunately no traces were cut, as is often done for customization, and putting everything back to the standard configuration was easy enough. But the machine was still not showing signs of life.

As I was pondering next steps, I put my finger on the CPU - if it was getting warm it could indicate it was at least doing something. Going over all chips that way, I also touched the PIO - wow! that thing got really hot! That didn't seem normal. Could it have shorted out? Fortunately, the [PIO and the CTC](https://www.8bit-wiki.de/fileadmin/8bit-wiki/IC-Datenblaetter/CPU/Z80/Z80_CPU_PERIPHERALS.pdf) - the most corroded of the chips - are optional on the MPF-1 and are not needed for basic operation. And they are socketed, so removing them was really straightforward.

And, HUZAAA! - the MFP-1 started up! 


<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210725_211404_.jpg?raw=true" alt="fig1" style="width: 400px;"/>


I got a new PIO and CTC, and for good measure brushed off as much of the corrosion on the pins of the socketed chips (with an antistatic brush evidently) and re-seated them. All seemed well: a working MPF-1.

As I was exploring which chip could be faulty it had occured to me that perhaps the previous owner had done something to change the monitor program - not very likely, since the original sticker was still on the [ROM](https://en.wikipedia.org/wiki/Read-only_memory). But I wanted to read it anyway. With the sticker in place, it was not possible to see what type of ROM it was. But I was assuming a 2532 ROM - a 32 kilobit or 4 kilobyte Read Only Memory, as this was the type the MPF-1B usually came with. 

I own two ROM programmers, the TL866 and the later TL866II+. These devices are difficult to beat for the capabilities you get for a very reasonable price. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210721_215835_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

But the TL866 and TL866II+ can not read the 25xx series of ROMs. It can read the 27xx series of ROMs though, which are very similar, apart from a few key differences. One of the differences is the that a few pins that have similar functions are in different positions, although 21 of 24 pins are in fact the same. To read the 2532 ROM, therefore, I could simply rewire three pins (and even get away with two if I wanted) to read it as if it were a 2732. 

I found a [rewiring solution](https://www.sinclairzxworld.com/viewtopic.php?t=3281):

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/2725adapter_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

I implemented it by bending the pins of a 24-pin socket and placing that on another. I believe I have gained extra vintage computing points for [wire wrapping](https://hjmegens.github.io/2021-11-04-Building-a-minimalistic-PDP11/) skills.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF/20210724_154206_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

I use the TL866 on my Linux systems, and the manufacturer-provided software only works for Windows. However, there is a great command-line based alternative: [Minipro](https://gitlab.com/DavidGriffith/minipro).

I read the ROM like this:

```

sudo minipro -p 'TMS2732A@DIP24' -r mpf-1b.bin

```

From a hexdump it seemed to confirm that the binary contained 4096 bytes of good data. But how to know if the ROM was original, and still good? For anything Micro-Professor related there is a great repository of articles, books and software maintained by [Fred Jan Kraan](https://fjkraan.home.xs4all.nl/comp/mpf1/). In fact it is the most extensive repository for this little SBC I've come across and a must-visit for every enthusiast. 

I found the [original MPF-1B ROM](https://electrickery.nl/comp/mpf1/doc/index.html) there, but in the [ihex format](https://en.wikipedia.org/wiki/Intel_HEX), and not the binary format that I just dumped from my own ROM. The ihex format has similar characteristics as the [papertape format used for KIM-1](https://en.wikipedia.org/wiki/MOS_Technology_file_format), but it is not the same. At that time, earlier this year, I had not yet made a [papertape converter for the KIM-1](https://hjmegens.github.io/2021-11-02-Making-KIM-papertape/) yet, or I could have probably easily adapted that to convert ihex. The biggest difference is that the ihex format specifies one of five different record types. 

There are other tools, however, that can convert ihex - it was a very common format. 

```

objcopy --input-target=ihex --output-target=binary monitor_and_tiny_basic.u6.hex monitor_and_tiny_basic.u6.bin


```

Computing md5sums showed the original and my ROM dump were identical:

```

hjm@XPS:MPF$ md5sum monitor_and_tiny_basic.u6.bin mpf-1b.bin 
b593fd21dfa3455681e29da494821e72  monitor_and_tiny_basic.u6.bin
b593fd21dfa3455681e29da494821e72  mpf-1b.bin

```

The ROM of my Micro-Professor therefore is the standard 4 KB monitor of the MPF-1B, as expected. Since I had a few 2532 ROMs lying around I tried to use my ROM programmer for programming in stead of reading the monitor software back on to one of those, using the same re-wiring contraption. But that didn't work out. These ROMs require 25V programming voltage, which the TL866 can't provide. The older TL866A maxes out at 21V, the newer TL866II+ at 18V. 

My joy of doing a quick and easy repair of my MPF was short-lived. After a few more hours working with the machine, it crashed, without any reason. As if to warn me it started beeping. It will reset alright, but after either a few minutes, or a few hours, will start getting fits. So, it is not happy. I've been investigating several leads and I will likely detail some of my findings in a later post. In fact I trained myself in using my new scope in the process. 

Often you will hear vintage computing enthusiasts recommending doing a complete recapping of a board. From what I learned from my investigations the caps are probably fine, and I don't want to remove all these nice tantalum capacitors unless necessary, that would seriously diminish the historic value for me. It is said that tantalum capacitors can 'auto-repair' as they are used after a long time. Maybe they can also deteriorate to a state that they fail intermittently, I don't know. Furthermore, the damp storage can have resulted in any number of issues, up to the point where chips degraded and need replacement. But again, I don't like these blanket replacement repairs - if I would remove all caps and all chips I would mostly be left with... a bare PCB (well, almost).

If I were to go back to a state like that for the board, in fact it would make much more sense to recreate the MPF altogether, designing a new PCB and all. All components are more or less easy to obtain so why not? Well, in the end this is what I ended up doing based on a design I found here. If anything, having a second MPF should make it easier to diagnose what ails my original MPF. So.... to be continued!



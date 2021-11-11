---
layout: post
title: Building an MPF-1B remake
tags: [Micro-Professor, MPF-1B, Z80, SBC, ROM]
---

Clones or remakes of historical computer designs offer ways to hack the old hardware without the risk of destroying an original machine. Here I detail the remake of a small single-board Z80 computer, the Micro-Professor 1B. 

## Recreating a Micro-Professor

In a [previous post](https://hjmegens.github.io/2021-11-08-Micro-Professor-1B/) I mentioned how I struggled with a MPF-1B that was not happy - it shows intermittent issues and I haven't yet been able to give a clear diagnosis of the problem. I've followed several leads, replaced and tested chips that are socketed, and I measured what I can with my scope. There are things that look odd, like these sharkfins on the data lines...

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211020_20114_.jpg?raw=true" alt="fig1" style="width: 300px;"/>

Could this be a problem? One serious limitation is that I don't have anything to compare to. (spoiler alert: with the remake done I now know that sharkfins are a normal feature for the MPF-1) 

For sure my original MPF-1B can be repaired - this particular design is simple, and all components that can fail can be easily replaced. The big chips are socketed, the smaller ones can be amputated, pins desolderd, and new sockets installed. And, of course, caps, diodes, resistors all can be desoldered and replaced. So, why don't I do that? Because that essentially means that my historic SBC would become a modern remake. A targeted repair is fine, but I just don't like blanket replacements of components in historical machines. 

But wait - if all components can be replaced, you *could* make a completely new MPF-1B, right? And that is true. However, you would need a circuit board of course. Which, theoretically I could design, because all the necessary information is available. But that, for now, is beyond me. Designing PCBs is high on my list of future projects, but I want to start with something a bit more doable. 

Doing clones and remakes has one additional advantage - building something yourself really is a great way to dive further into the details of a design. This is one thing I learned from the [Outstanding PAL-1](https://www.tindie.com/products/tkoak/pal-1-a-mos-6502-powered-computer-kit/), a KIM-1 clone, which potentially may be better than the real thing (I wouldn't know because I don't own an original one).

Fortunately, [Ruud Baltissen](http://www.baltissen.org/newhtm/myself.htm) pointed me towards [a site, in Portuguese](https://ekaww4.wixsite.com/tecnologia/projeto-cedm-80), from a Brazilian enthusiast who had recreated several historic SBCs, including the MPF.

Interestingly, the MPF-1 is called CEDM-80 in Portuguese. The project looked brilliant, but it was impossible to find Gerbers on the site. I therefore decided to contact the creator, Emilio, directly. Although distributing the Gerbers was not possible, he offered to order a batch from JLPCB at cost and have it sent to me directly.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211107_101311pcb_.jpg?raw=true" alt="fig1" style="width: 400px;"/>

Emilio also sent me a BOM, so I could already start ordering parts in the mean time. Interestingly, there aren't any really 'difficult' parts there. Most of it can be really easily ordered from eBay or Aliexpress. That includes the 6116 RAM, Z80 CPU, PIO and CTC. One thing I have decided not to save money on anymore, though, are caps. After ordering many batches of cheap caps that all are significantly out of spec I decided it's not worth the trouble and ordered some good quality caps from Mouser. I wish I had done the same for the IC sockets.

There are three ICs that require some attention though. The first two are the drivers for the LED displays. These are the 75491 and 75492. Looking around for these on eBay and elsewhere it became clear these display driver chips could potentially be the most expensive parts of the whole board! I managed to find a seller in Croatia that had some for a reasonable price. But then Emilio very helpfully pointed out where he got his from AliXpress, for even less. I ended up buying two batches and perhaps that may have been a good thing, but more on that later.

####  MPF-1 remake

| ITEM   |    ID     |     DESCRIPTION     | QTY || ITEM   |    ID     |     DESCRIPTION     | QTY |
|---------|:----------|:---------------------|-----||---------|:----------|:---------------------|-----|
| 1 | U1 | Z80A-CPU | 1 || 22 | RA1/RA2 | 9X10K (RESISTOR ARRAY) | 2 |
| 2 | U2 | 74LS74 | 1 || 23 | RA3 | 8X10R | 1 |
| 3 | U3 | 74LS14 | 1 || 24 | RA4 | 8X100R | 1 |
| 4 | U4 | 74LS90 | 1 || 25 | Q1 | 7805 | 1 |
| 5 | U5/U9 | 74LS139 | 2 || 26 | Q2,Q3 | 2N3906 | 2 |
| 6 | U6 | 2732 | 1 || 27 | CR1/CR2 | 1N4148 | 2 |
| 7 | U7/U8 | 6116 | 1 || 28 | CR3 | 1N4734A | 1 |
| 8 | U10 | Z80 PIO (OPTIONAL) | 1 || 29 | XTAL | 3,579545MHz | 1 |
| 9 | U11 | Z80 CTC (OPTIONAL) | 1 || 30 | | LED (GREEN/RED) | 2 |
| 10 | U12/U15 | 75491 | 2 || 31 | U16-21 | PHD560 Display- Catode | 6 |
| 11 | U13 | 75492 | 1 || 32 | | SPEAKER, 8 Ohms | 1 |
| 12 | U14 | 8255 | 1 ||33 || KEYS (KEYBOARD) | 36 |
| 13 | R9| 100| 1 || 34| |power connector | 1 |
| 14 |R6-7 |1K | 2 ||35| | 3.5 mm audio connector | 2|
| 15 | R1-4/R11-12 | 10K | 6 ||||||
| 16 | R8/R10/R13-14 | 330R | 4 ||||||
| 17 | C15 | 100pF | 1 ||||||
| 18 | C9/C14/C6 | 4,7uF | 3 ||||||
| 19 | C11-13 |103 (0,01uF) 3|||||||
| 20 | C7 | 470pF | 1 ||||||
| 21 | C1-5/8/10 | 104 (0,1uF) | 7 |||||||

The third chip that required some consideration was the ROM. The original MPF-1B makes use of the 2532. I have a few of these lying around, so I wouldn't need to buy any. But I haven't found a modern programmer that can program the 2532. The biggest problem is that programming requires 25V. Using the 2532 in a MPF-1B remake would therefore be very impractical. In the MPF-1 there is a set of jumpers you can set though to switch between ROM types, so you can use another ROM, the 2732. That does, however, involve cutting some traces. In the design of the remake Emilio had already taken care of all this - the MPF-1 recreation uses the 2732 as standard! Brilliant! Although I have a few of the 2732 lying around, I still decided in the end to buy some. The reason is that the 2732 I owned can only be programmed at 21V. I have an older verions of the TL866 programmer, the TL866A, that can do it, but my more recent TL866II+ can't. The [NMC27C32BQ](https://pdf1.alldatasheet.com/datasheet-pdf/view/85625/NSC/NMC27C32BQ.html) is a 2732 version that is still widely available, while being programmed at a much lower voltage (12.5V). Having this type of 2732 as the basis for my project should make me less relying on ROM programmers that are difficult to find (the TL866A no longer being manufactured).

As the PCBs and components started to come in, the moment to start building came near. There were still a few components on their way, such as for instance the audio connectors, but everything that was essential for basic operation was there.

One of the nice features of not having any build instructions is that you really have to pay attention to the schematics. What goes where? And why? This really makes you wonder how different parts of the MPF-1B work. For instance, the CPU clock is running at bout 1.79 Mhz, but the crystal is just under 3.58 Mhz - how is the frequency halved, and why? I decided to replicate the clock circuitry on a breadboard to find out (more details in a later post, maybe). As a bonus, the breadboarded circuit provided a nice tester for the crystal before soldering that in. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20210926_134207_.jpg?raw=true" alt="fig1" style="width: 300px;"/>
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/clock_mpf.png?raw=true" alt="fig1" style="width: 300px;"/>

I also wasn't completely sure whether the LED displays would work - Emilio had recommended a certain LED display but they hadn't come in yet. I had a few common cathode displays lying around but were they the right type? I also was considering the values of some of the resistor arrays - investigating my ailing MPF-1B I noted some pretty sharkfins on the scope, which likely resulted from fairly high pullup resistor values. Could be interesting to play with these values? So, apart from the ICs I also socketed the displays, resistor arrays, and even the transistors. This first build therefore allows for some experimentation before I build another one. 

Assembling the board was quite a bit of work, one evening and one morning. I'm not that fast and I check every component as much as I can e.g. with a component tester for resistors, caps, LEDs, diodes, and transistors. The smaller ICs and 6116 RAM were tested with my [ Awesome Chiptester Pro](https://8bit-museum.de/sonstiges/hardware-projekte/hardware-projekte-chip-tester-english/). And the CPU was tested in a minimal RC2014 setup. I also tested all keyboard buttons, all 36, before soldering. 

Assembly went well, but there were few small hickups. The only one to note, though, is that in retrospect I should have chosen better quality sockets for the larger ICs. After soldering the sockets in, it already was apparent that the individual metal sockets were not all level. I should have paid better attention, but this has never occured to me before. The sockets used for this project certainly were the cheap kind. At one point I wanted to replace the CPU, tried to pull it out but one of the legs wat really stuck in the socket. In trying to get it unstuck I accidentally broke of the leg of the CPU. Doh! The price of going cheap.

A few other components had not arrived yet - the audio sockets and the speaker. The speaker is important because the MPF likes to talk to you. Beeps of 1000 and 2000 hz is how it speaks. I cannibalized an other project for a speaker that was too big but would do for now, while waiting for my teensy 23mm wide speakers to arrive from China. The audio sockets on the other hand could wait.

And the build was completed! Now all I needed to do was prepare the ROM. I programmed it with the monitor binary I created in [the previous post](https://hjmegens.github.io/2021-11-08-Micro-Professor-1B/).


```

sudo minipro -p "NMC27C32BQ@DIP24" -w monitor_and_tiny_basic.u6.bin

```

And then stuck it in the leftmost memory socket. Would it work? ..... I connected a 9V power supply and .... TADAAAA! it worked!

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211019_132711_.jpg?raw=true" alt="fig1" style="width: 500px;"/>


I started punching in a few simple examples from the [User's Manual](https://electrickery.nl/comp/mpf1/doc/MPF-1_usersManual.pdf), including the HELP US blink example and the alsways fun police siren. With the latter, it became quite apparent that sound did work but... it was really very muted. It turned out that for R9 I had used a 1K resistor. The PCB notes '680' for that resistor, and I thought that 1K should be fine too. However, after a bit of further research it became apparent that in the MPF the resistor value is rather 68 Ohm. I replaced the 1K resistor with one of value 100 Ohm, and then the speaker was blaring really nice and loud. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211019_132809_.jpg?raw=true" alt="fig1" style="width: 500px;"/>

From what I could see, everything worked. There was however still the issue of the unpopulated middle memory chip location, which can either hold a 6116 RAM chip, thereby increasing total RAM from 2 to 4 KB, or another ROM, which, in the original MPF-1B can be either of the 2532 or 2732 type. 

The configuration for that in the original MPF-1B is to cut a trace and set jumpers. However, Emilio already had changed the original design to anticipate either 6116 RAM or 2732 ROM, so the only trace I had to cut was the one between jumpers 1 and 2 , which is a default 'jumper closed' setting specific to this remake's design. The only other thing to do is to connect positions 2 and 3. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211107_102500_.jpg?raw=true" alt="fig1" style="width: 250px;"/>
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211107_102545_.jpg?raw=true" alt="fig1" style="width: 250px;"/>


For the ROM I thought it would be nice to have two test programs that could provide some feedback from the LED display (the flashing 'HELP US' example and the noisy police siren from the User's Manual). I coded them so that they would be compiled as one binary. I aim to make a tool myself that can stitch binaries together with proper numbers of zeros inbetween. But that's for another day. Note that the ROM originates at location 0x2000 in the MPF memory space. 


```

  .org $2000         ; Flash 'HELP US'
  ld HL, blank       2000:  21 26 20
  push HL            2003:  E5
  ld IX,help         2004:  DD 21 20 20
loop:
  ex (SP),IX         2008:  DD E3
  ld B,50            200A:  06 32
helfseg:
  call SCAN1         200C:  CD 24 06
  djnz helfseg       200F:  10 FB
  jr loop            2011:  18 F5
F00:0015       ;
  .org $2020
help:
  defb $0ae          2020:  AE         ;'S'
  defb $0b5          2021:  B5         ;'U'
  defb $01f          2022:  1F         ;'P'
  defb $085          2023:  85         ;'L'
  defb $08f          2024:  8F         ;'E'
  defb $037          2025:  37         ;'H'
blank:
  defb 0             2026:  00
  defb 0             2027:  00
  defb 0             2028:  00
  defb 0             2029:  00
  defb 0             202A:  00
  defb 0             202B:  00
SCAN1 .equ  $0624

.org $2050           ; Police car siren
loop2:
  ld C,0             2050:  0E 00
  ld HL,$0C0         2052:  21 C0 00
  call TONE          2055:  CD E4 05
  ld C,$0C0          2058:  0E C0
  ld HL,$100         205A:  21 00 01
  call TONE          205D:  CD E4 05
  jr loop2           2060:  18 EE
TONE .equ $05E4

  .org $2FFF         2FFF:  00
  defb 0
  end

```

I've really come to appreciate [VASM](http://www.compilers.de/vasm.html) as compiler of choice for my 8-bit projects. I had previously used it for my [Bean Eater](https://eater.net/6502) and [PAL-1 projects](https://hjmegens.github.io/2021-11-06-The-PAL1-RIOT-expansion/). When I was searching for a Z80 assembly code compiler I discovered I was already using one: VASM. 


```

VASM/vbcc/bin/vasmz80_oldstyle -Fbin -dotdir helpus.s -o helpus_police_siren.bin -L helpus_police_siren.lst

```

Important to note is that despite the apparent start at 0x2000 the code starts at byte zero of the binary. The binary is 0x1000 or 4KB in size and is filled with 0s until the end. The reason for that is the zero deposited at position 0x2FFF. Some ROM programmers don't like it when the size of the binary is different than the size of the ROM. By applying this little trick the binary is exactly the same size and all empty spaces are filled with zeros. The Minipro programmer software, for instance, will complain if the binary is smaller, will nevertheless do it when you tell it to, but the empty space will be 'blank', i.e. 0xFF for every blank byte since bits in a UV erased EPROM will be in the '1' state. 

```

hjm@XPS:example_code$ hexdump -C helpus_police_siren.bin
00000000  21 26 20 e5 dd 21 20 20  dd e3 06 32 cd 24 06 10  |!& ..!  ...2.$..|
00000010  fb 18 f5 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000020  ae b5 1f 85 8f 37 00 00  00 00 00 00 00 00 00 00  |.....7..........|
00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000050  0e 00 21 c0 00 cd e4 05  0e c0 21 00 01 cd e4 05  |..!.......!.....|
00000060  18 ee 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00001000



```

Subsequently, I programmed the second 2732 ROM:

```

sudo minipro -p "NMC27C32BQ@DIP24" -w helpus_police_siren.bin 

```

I plunked the ROM in the middle slot, put power to the MPF-1B remake, selected address 0x2000 and pressed 'Go', and it worked, a blinking 'HELP US' on the display. Selecting 0x2050 and hitting 'Go' the little machine would do it's police siren routine. The middle slot seemed configured properly therefore. 

So, now I can finish this board. I found a cute little 23 mm speaker that fits brilliantly, and, although only 0.5W is loud enough. Finally all components have come in. Including that second batch of 75491 and 75492 LED drivers. I've always found the LED displays of my original one very faint, but the remake wasn't that much brighter. However, when I replaced the driver ICs I got from the Croatian vendor for the Chinese one, the difference was quite dramatic. The displays were VERY much brighter. Actually, to the point that I'm not even sure if they work as intended because even when the displays are supposed to be 'off' there is a tiny bit of bleeding light. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211107_102644_.jpg?raw=true" alt="fig1" style="width: 300px;"/>

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211109_214818_.jpg?raw=true" alt="fig1" style="width: 600px;"/>


From the figure above the careful observer may have spotted I've started fitting one audio connector. Actually I had fitted both, in a tentative fashion, to discover an issue when I started playing with the audio to transfer programs. But that is for another log. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/MPF_remake/20211107_101311_.jpg?raw=true" alt="fig1" style="width: 600px;"/>

I'm looking forward to make a second MPF-1B remake. The minimum order is 5 boards, and I have enough parts left for at least one more. I will for sure buy some quality IC sockets first though. But I want to experiment with the first one before deciding on a specific configuration for a second. That includes the keyboard. Although the remake's keyboard is surprisingly usable, it may be possible to make it look a bit more like the original. 

For now I'm superhappy with this board, because it immediately set me on a path of hacking it. Perfect: this is precisely what these boards were designed for, over 40 years ago. And now I can do that without risking destroying my original. 

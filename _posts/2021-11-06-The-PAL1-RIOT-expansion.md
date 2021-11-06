---
layout: post
title: The PAL-1 RIOT expansion
tags: [KIM-1, PAL-1, RRIOT, RIOT, I/O programming, timer functions]
---

The 8-bitters of the mid-'70s came with a bunch of general and specialized support chips. The KIM-1 used the RRIOT. In this post I'm describing my first steps in programming the PAL-1 6532 RIOT I/O and timer functions.

## Programming the PAL-1 RIOT expansion

One of the interesting aspects of the first generation of microprocessors is that they generally were introduced together with a bunch of 'support chips'. For instance, for the very first microprocessor, the [Intel 4004](https://en.wikipedia.org/wiki/Intel_4004), there were a number of [support chips](https://en.wikipedia.org/wiki/Intel_4004#Support_chips) dedicated to extend the capabilities of the microprocessor.

Newer generations of 8-bit microprocessors, from the mid-'70s onward, including the [8080](https://en.wikipedia.org/wiki/Intel_8080), [Z80](https://en.wikipedia.org/wiki/Zilog_Z80), [6800](https://en.wikipedia.org/wiki/Motorola_6800), and [6502](https://en.wikipedia.org/wiki/MOS_Technology_6502), all increasingly had some capabilities already built in, but nevertheless came with a range of support chips of their own to support a number of general or more specialized tasks. 

For the Z80, for instance, these support chips included the PIO, SIO, and CTC, to support parallel and serial interfacing, and to provide timer function, respectively.

The MOS 6502 saw a similar development of general support chips, including the VIA (6522), found in the Commodore VIC20 and 1541 disk drives, for instance, and CIA (6526) found in the Commodore 64. As homecomputers became more versatile in the early '80s, the range of specialized support chips, such as for instance the SID (6581) and VIC and VICII, also expanded. 

One support chip for the 6502 that is particularly interesting is the [RRIOT (6530)](https://en.wikipedia.org/wiki/MOS_Technology_RRIOT). **RRIOT** stands for **ROM-RAM-I/O-Timer**. This is a rather exact description of the functions encapsulated into this one chip: it provided 2 banks of 8 general input/output pins, had a timer function, and provided a teensy bit of RAM. The RAM was only 64 bytes, but when you are working on a system that only has 1KB of RAM on board, as was the case with the KIM-1, those 64 bytes could come in handy for writing applications (see the story of KIMVenture).

figures/RIOT/FBoK_title.png
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/RRIOT_block_diagram.png?raw=true" alt="fig1" style="width: 500px;"/>

Importantly though, the RRIOT also provided read-only-memory (ROM), of 1KB in size. The KIM-1 has two RRIOTs, and therefore the KIM-1 monitor is divided in two parts that are each stored in a different RRIOT (the 6530-002 and 6530-003 monitor programs). The RRIOTs could not be re-programmed, so the ROM was programmed in the factory which made the RRIOT a very specialized device. In fact you couldn't make a KIM-1 clone nowadays if it weren't for a very cool and klever solution, based on a similar chip. 

Apart from the 6530 RRIOT, MOS also designed a chip that was functionally similar but did not have the ROM. This was the [6532] RIOT, note the one missing **R** for **ROM**. Apart from the missing ROM, the RAM was expanded to a whopping 128 bytes, and the pinouts were different. The realization came from [Ruud Baltissen](http://www.baltissen.org/newhtm/6530repl.htm) that, combining the more general-purpose 6532 with an external ROM, the specialized functions of the 6530 - in fact of ANY type of 6530 - could be replicated. A stroke of pure genious, and the basis of every KIM-1 replica or clone afterwards. The 6532 is still available although no longer produced, contrary to the KIM-1 RRIOTs, which are pure Unobtanium. But that no longer matters, we can still have KIM-1 clones such as the [Fabulous PAL-1](https://www.tindie.com/products/tkoak/pal-1-a-mos-6502-powered-computer-kit/). 

The interesting aspect of the two RRIOTs in the KIM-1 is that the second one is actually not really needed apart from the ROM. It adds general input/output, and additional timer functions that are used by some programs (e.g. the Timer utility from the [First Book of KIM](http://retro.hansotten.nl/uploads/files/The%20First%20Book%20of%20KIM.pdf), more about that later). Therefore, having an external ROM big enough for both monitor programs, and the 128 in stead of 64 additional bytes that the 6532 provides, you can in fact make a completely functional KIM-1 clone based on a single 6532. This was the strategy followed by [Liu Ganning](https://twitter.com/LiuGN) to create the PAL-1, making it compact and extremely affordable, because, although the 6532 is still available, it is not exactly a common chip, nor is it still in production, unlike, for instance, the 6522 VIA (in the form of the 65c22). 

Nevertheless, some KIM-1 programs rely on having the second RIOT there, especially because of it's timer functions. In addition, since the first RIOT has all it's I/0 tied up for interfacing (keyboard, display, and serial) the PAL-1 does not offer out-of-the-box I/O capabilities to connect to the wider world. But fortunately, Liu created a bunch of rather brilliant expansion modules, including a module that can provide that second RIOT. And this RIOT expansion module was the one I wanted to get going with. My learning goals here were to understand the I/O and timer functions of support chips in general, as I had seen these before but am still grasping to understand how they work and how they can be programmed.

Recently I had been tinkering with a few other projects. Two that are important to note here are the Ben Eater 6502 and the Micro-Professor. The [Ben Eater 6502](https://eater.net/6502) has taught me more of 8-bit microprocessor programming than any other project so far. It goes into the details of the 65c02 behaviour and machine code processing, and by one-by-one adding RAM, ROM, and an interface chip (the 65c22 VIA), you learn how each of these components work. One eye-opener for me was that the VIA works by writing to a few memory addresses which are mapped on the 65c02 memory space. One address that specifies, for 8 output pins, whether they are treated as input or output, and one address location that can be either read or written, that contains the state of each of the output pins (a digital one or zero). In the case of interfacing with LEDs, you simply set the direction register to output, and subsequently write 0xFF (or binary 11111111) to the I/0 register if you want all 8 LEDs to switch on.

The [Micro-Professor](https://en.wikipedia.org/wiki/Micro-Professor_MPF-I), or [MPF-1](https://fjkraan.home.xs4all.nl/comp/mpf1/)), on the other had, has a few optional chips that peaked my interest. First of all it has the PIO, which has a similar function as the VIA, and which can be used to interface with the outside world pretty much the same way. Furthermore, the MPF-1 sports a CTC, which provides timing functions. But to this day I haven't really had the chance to explore these, because my vintage MPF-1 is not happy for reasons I haven't found out yet (an intermittent issue). For some serious tinkering with the MPF-1 I've recently built a remake - I'll detail that project in a later post).

Enter the PAL-1, with the 6532, that combines those functions. I recently finished the complete expansion set for the PAL-1, including the cassette interface and the RIOT expansion board. Now it was time to experiment a little with that.

The first thing that came to mind was simply replicating Ben Eater's simple program to write an alternating pattern to the LEDs (01010101 followed by 10101010). The rationale for writing to locations in the 6502 memory space is the same as what Ben Eater taught me for the VIA. In the KIM-1 the memory locations for the RRIOTs are specified in the KIM-1 manual:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/RRIOT_memorymap1.png?raw=true" alt="fig1" style="width: 500px;"/>

For the PAL-1 expansion RIOT, this means the important addresses are located between 1700 and 170F

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/RRIOT_memorymap2.png?raw=true" alt="fig1" style="width: 500px;"/>

I hooked up PA0 to PA7, and the GND pin from the expansion board to a breadboard with 8 red LEDs, and a 9-pin 470 Ohm rate limiting resistor array. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Photos/20211106_145047.jpg?raw=true" alt="fig1" style="width: 500px;"/>

If I want to interface with 8 LEDs, I can for instance use the A register, write 0xFF to location 1701 to specify all I/0 pins to output, and then write a pattern, for instance 0x55, for 01010101, to location 1700.

And this works! 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Photos/20211106_143921.jpg?raw=true" alt="fig1" style="width: 500px;"/>

Switching to the opposite pattern simply requires writing 10101010, or 0xAA, to location 1700.

Now I can make a simple program to do this in an alternating fashion:

```

  .org $200         ; start at 0x200
  lda #$FF          
  sta $1701         ; set A data register to output
loop:
  lda #$55
  sta $1700         ; output 01010101 on A data register
  lda #$AA          
  sta $1700         ; output 10101010 on A data register
  jmp loop          ; and back to loop
  
```
This program works alright but when I start it on my PAL-1 all I notice is a slight dimming of the LEDs (when starting the PAL-1 location at 1701 often is 0, keeping all output pins in a high state). So something happens. And it is clear what's the problem here. While in my Ben Eater 6502 computer I have the option to use a very slow clock (clock speeds from about 1hz to 500hz, and manual), the PAL-1 clock runs at 1Mhz. Everything just happens to fast to see anything, with the leds being 'on' about half the time, hence the dimming.

With no option to slow the clock down, there is of course the option of single stepping but a more interesting solution would be to let the PAL-1 sleep for 1 second between transitions of the LED pattern. But how to do that? My first thought was to have half a million NOPs (for No Operation instruction), as each of these takes two clock cycles to complete which would keep the PAL-1 busy for a second. But that seemed rather complicated as well. Then it occurred to me that of course, the RIOT provides timer functions. This seemed complicated, but doable. It requires writing and reading to and from specific addresses in the timer registers, but I really didn't understand how that would work. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/KIMtimer_address.png?raw=true" alt="fig1" style="width: 500px;"/>

Maybe I needed an example to work from. When I completed the board one of the programs I used to test the RIOT expansion worked was the 'Timer' program from the First Book of KIM (FBoK). 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/FBoK_title.png?raw=true" alt="fig1" style="width: 400px;"/>

You can turn your KIM-1 into a stopwatch. The author even details he made the intervals precise to 1 second up to 50 microseconds. And, crucially, it makes use of the same (R)RIOT I wanted to use for the I/O, so maybe I could use some tricks to achieve two learning goals in one go: using the RIOT for I/O **AND** timer functions. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Timer1.png?raw=true" alt="fig1" style="width: 400px;"/>

Studying how the author created a timer taught me it involved writing a value to the timer register (address 1706)), subsequently read it (address 1707) until the timer indicated it counted back to zero. And then doing this 100 times for a total of 1 second. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Timer2.png?raw=true" alt="fig1" style="width: 400px;"/>

The interesting value here is 9C. That value is written to address 1706, and, once written, will be decremented by one every 64 clock cycles. If the count is back to zero, reading address 1707 will result in a byte with a '1' on location 7, and if the count is not zero, then reading 1707 will result in a zero. 

Every count back from 9C back to zero therefore requires 64 * 0x9C or 9984 clock cycles, of 1 microsecond each, or just under 10 ms in total. Doing this 100 times totals 0.9984 second. Close enough. (If I would use 0x9D in stead I would overshoot, totaling 1.048 seconds; 0x9C is the closest you can get to 1 second therefore, at least in this simple way, and it is certainly close enough for my current purpose)

Unfortunately, the expansion RIOT does not offer interrupt functions, which means that during the wait you can't do other stuff, but that's ok. The only important thing for this program is that the PAL-1 won't do anything useful for 1 second between the LED pattern changes. 

I ended up implementing the wait function as a subroutine that would be called twice within the loop defined earlier. The subroutine initializes the timer by writing 0x9C to location 1706, initializes an extra counter to count to 100, and then starts reading location 1707. Once it counts back to zero, the timer is re-initialized, the extra counter is incremented by one, and this is done 100 (or 0x64) times. When the count to 100 is completed, the subroutine returns to the main program.

The final assembly code looks like this:

```

; blink.s - blink 8 LEDs on PAL-1's expansion 6532 RIOT

  .org $200         ; start at 0x200
  lda #$FF          
  sta $1701         ; set A data register to output
loop:
  lda #$55
  sta $1700         ; output 01010101 on A data register
  jsr sec           ; wait 1 sec
  lda #$AA          
  sta $1700         ; output 10101010 on A data register
  jsr sec           ; wait 1 sec
  jmp loop          ; and back to loop

; subroutine to wait one second 
sec:
  lda #$9C          ; count back from 156
  sta $1706         ; through RIOT interval timer
  lda #0
  sta $0            ; initialize extra counter
clk:
  lda $1707         ; get timer status
  beq clk           ; check if counted back to 0 yet
  lda #$9C       
  sta $1706         ; re-initialize RIOT 64 counter register
  inc 0             ; increase extra counter by 1
  lda 0 
  cmp #$64          ; extra counter already 100?
  bne clk           ; if not, extra interval timer cycle
  rts               ; return from subroutine
  
```

I compiled the code using [VASM](http://www.compilers.de/vasm.html). It is the compiler Ben Eater uses in his 6502 project. On the one hand it is fairly straighforward in its use - none of the linker stuff for instance sometimes found in some assemblers. Perfect for somebody like me who's not yet too versed in the ways of assembly programming. At the same time, VASM is very flexible and can for instance also work with Z80 assembly code, which makes it perfect for many of my projects. 

Compiling was done to include the start address in the CBM 'PRG' format:
```

../VASM/vbcc/bin/vasm6502_oldstyle -Fbin -dotdir blink.s -o blink.bin -cbm-prg -L blink.lst

vasm 1.8k (c) in 2002-2021 Volker Barthelmann
vasm 6502 cpu backend 0.9c (c) 2002,2006,2008-2012,2014-2021 Frank Wille
vasm oldstyle syntax module 0.16 (c) 2002-2021 Frank Wille
vasm binary output module 2.1 (c) 2002-2021 Volker Barthelmann and Frank Wille

seg200(acrwx1):	          52 bytes

```
The hexdump reveals that, indeed, the start address is included
```

hjm@XPS:RIOT_expansion$ hexdump -C blink.bin 
00000000  00 02 a9 ff 8d 01 17 a9  55 8d 00 17 20 18 02 a9  |........U... ...|
00000010  aa 8d 00 17 20 18 02 4c  05 02 a9 9c 8d 06 17 a9  |.... ..L........|
00000020  00 85 00 ad 07 17 f0 fb  a9 9c 8d 06 17 e6 00 a5  |................|
00000030  00 c9 64 d0 ee 60                                 |..d..`|
00000036

```

The byte code relates to the assembly code as follows:

```

  lda #$FF          0200:  A9 FF
  sta $1701         0202:  8D 01 17
loop:
  lda #$55          0205:  A9 55
  sta $1700         0207:  8D 00 17
  jsr sec           020A:  20 18 02
  lda #$AA          020D:  A9 AA
  sta $1700         020F:  8D 00 17
  jsr sec           0212:  20 18 02
  jmp loop          0215:  4C 05 02
sec:
  lda #$9C          0218:  A9 9C
  sta $1706         021A:  8D 06 17
  lda #0            021D:  A9 00
  sta $0            021F:  85 00
clk:
  lda $1707         0221:  AD 07 17
  beq clk           0224:  F0 FB
  lda #$9C          0226:  A9 9C
  sta $1706         0228:  8D 06 17
  inc 0             022B:  E6 00
  lda 0             022D:  A5 00
  cmp #$64          022F:  C9 64
  bne clk           0231:  D0 EE
  rts               0233:  60

```
Subsequently, I converted the binary to a papertape format using my 'virtual papertaper'. The default of that program is accepting the binary as a PRG, i.e., including the start address, but it is versatile enough to also accept a plain binary with the start address provided by the user. 

```

python ../PAL1_tools/prg_to_ptp.py -i blink.bin -o blink.ppt

```
The resulting papertape format:
```

;180200A9FF8D0117A9558D0017201802A9AA8D00172018024C050206C7
;180218A99C8D0617A9008500AD0717F0FBA99C8D0617E600A500C90A48
;04023064D0EE6002B8
;0000030003

```
Finally, the papertape was sent to the PAL-1:

```

ascii-xfr -s -l 100 -c 10 blink.ppt >/dev/ttyUSB0

```
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Photos/20211106_144236.jpg?raw=true" alt="fig1" style="width: 500px;"/>

And...! Success! The LED pattern alternates with a near-exact 1 second interval. 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Photos/20211106_144613.jpg?raw=true" alt="fig1" style="width: 300px;"/>
<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/RIOT/Photos/20211106_144614.jpg?raw=true" alt="fig1" style="width: 300px;"/>


I posted some footage on Twitter.

This was a nice learning experience, and I'm now set to explore more ways to interface to the real world with my PAL-1. I'm also very interested in exploring timer functions further. 

The code for this project can be found in [my PAL-1 Github repository](https://github.com/hjmegens/PAL1_tools).

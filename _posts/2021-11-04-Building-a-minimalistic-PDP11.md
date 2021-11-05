
---
layout: post
title: Building a minimalistic PDP-11
tags: [PDP-11, Qbus, electronics]
---

# Building a minimalistic PDP-11

*This post is based on a series of posts at the Dutch Vintage Computer Collectors Mailing List (CVML), between October 16 and December 17, 2020*

Somewhere October last year I saw an add for a nice computer terminal. I contacted the seller and we soon reached an agreement. It turned out that the seller was Roland Huisman ([his youtube channel](https://www.youtube.com/user/proxxima038/videos)). We started chatting over whatsapp, and through the Dutch Computer Collectors Mailing List he knew already I was interested in [PDP-11 computers](https://en.wikipedia.org/wiki/PDP-11). At that time the only PDP-11 'hardware' I owned was the [PiDP-11](https://obsolescence.wixsite.com/obsolescence/pidp-11-overview) (note: that picture at Oscar's site with the amber VT220, that's mine!). However awesome the PiDP-11 is, though, it is not quite the real deal. 

So as we set a time for me to pick up the terminal (more on that machine in a later post), Roland asked if I was interested in building a minimalistic PDP-11. If so, he would have a few Qbus boards for me to start with. And of course, that certainly did peak my interest, I had never considered that would even be possible! 

Roland sent me some links to study:

* [Minimal system from Henk Gooijen's webstie](http://www.pdp-11.nl/ba11-boxes/ba11va/ba11va-info.html)
* [Minimal PDP-11 system from Diane Neisius' site](http://www.diane-neisius.de/pdp11/index_E.html#base)
* [Malcolm's minimal PDP-11 system](http://avitech.com.au/?page_id=1657)

It hit me that those minimalistic setups were in a very real sense very similar in modularity to the RC2014 I had already some experience with. Minimalistic it would be, certainly, no fancy blinkenlights and cool-looking frontpanel with switches. But the open, minimalistic design immediately struck a chord because I really like single board (or modular) computers. But: with NO meaningful experience, could I really pull this off? Roland convinced me that I could. 

So, that evening I went home not only with a cool terminal, but also with three boards Roland generously donated to a budding PDP-11 enthusiast: one LSI cpu board, a serial board, and some RAM. I was over the moon: my first REAL PDP-11 hardware!

But Roland had no spare Qbus backplanes, so that was going to be the next hunt (off topic, but relevant: after a year of hunting down Qbus components, I have discovered that backplanes are by far the hardest to get). I sent out a request for a backplane to the Computer Collectors Mailing List, and, fortunately, Camiel Vanderhoeven had a spare backplane. And apart from the backplane, he also offered a tour through his [VAXBARN](https://www.vaxbarn.com), and actually donated a few more boards to get stared. 

Now, I should be good to go: I had a bunch of boards, a rack, and had purchased a modern, stable AT PSU so as not to blow up my precious vintage boards due to a faulty power supply. Here's a picture of my collection of PDP-11 stuff at the time - note the PiDP-11 for scale!

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/GetGoing.jpeg?raw=true" alt="fig1" style="width: 400px;"/>

However, I really, really didn't know anything about PDP-11 computers, and overlooked one very important aspect: the control of powerup, the Line Time Clock, and things like halt and reset signals. Remember, this would be a completely bare setup, no front panel of any sort, and not even an original PSU. Fortunately, it was pointed out to me by several people, that in my blind enthusiasm I forgot to study a few things. The site of [Malcolm](http://avitech.com.au/?page_id=1657) offered a fairly straightforward solution: use an Arduino to control the PSU, provide a LTC, provide the power good signal to the PDP-11 CPU, deal with the halt signaling, and report some basic signals using LEDs. 

After building the Arduino-based frontpanel, and checking out powerlines etc, the next thing to do was to configure the serial connection, because without it, there would be no way to talk to the CPU. The PDP-11 clone serial board I initially got, however, was beyond my skills to figure out, and there was no real documentation to be found (since then, Roland has actually figured this board out, without any documentation - bona fide retro-wizzard stuff). I got a [DLV11-J](http://www.bitsavers.org/pdf/dec/qbus/EK-DLV1J-UG-001_DLV11-J_Users_Guide_Oct78.pdf) from Camiel and for obtaining documentation for that board is easy. I purchased a [cheap wire-wrapping tool](https://www.amazon.com/dp/B00BFYE0CY/ref=cm_sw_r_tw_dp_TSMK3X1NPMV7M985F2K4?_encoding=UTF8&psc=1) to reconfigure the jumper settings the 'vintage way'. I ended up setting everything back to factory resets. When you have no clue what you're doing, going back to basics seemed the safest choice. Subsequently for figuring out how to manage my RS232 adapter connection, I got help from [Fred Jan Kraan's site](https://fjkraan.home.xs4all.nl/comp/minc/index.html).

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/Figure_out_serial.jpg?raw=true" alt="fig1" style="width: 400px;"/>

The moment of truth had arrived. Now I had everything assembled, in a minimal configuration: backplane, CPU, serial board, PSU, and the Arduino-based frontpanel. I fired her up and.... nothing. Niks. Rien du tout. Nada. Zilch. 

With my limited experience I had very few leads to go from. Of course I measured all the control signals, and rechecked configurations. Everything seemed ok. Next I followed a lead from [Joerg Hoppe](http://retrocmp.com/), to check data/address lines for activity. So, I did, with my [Saleae Logic 8](https://www.saleae.com), and it became clear that the CPU was not doing anything. 

So, could the CPU board be faulty? I had gotten another generous donation of an LSI CPU board from [Geert Rolf](http://www.bejaardecomputers.nl/en/WIE/index.html), so replaced the board, but same result. What were the odds of multiple boards being faulty? I studied the [M7270 manual](http://bitsavers.org/pdf/dec/pdp11/1102/EK-LSI11-UG-001_KD11-HA_User_Guide_197712.pdf) in detail for any leads, but in the end it was again Joerg Hoppe who provided some guidance by pointing out that there are some incompatibilities between these earlier LSI Qbus boards and later Qbus CPUs. Although the backplane *should* be compatible, there could still be something overlooked. I had, in the mean time, purchase a few more Qbus boards for a reasonable price, including a later 'J' board, the [KDJ11A](https://gunkies.org/wiki/KDJ11-A_CPU). I actually also managed to get a board extender which would allow me easy access to the serial board if necessary. I set everything up... 

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/FirstLight_setup.jpeg?raw=true" alt="fig1" style="width: 400px;"/>

Then started the PDP-11 up again, aaaaand....

The Arduino-based console indicated the machine was running!!!

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/Minimalist_console.jpeg?raw=true" alt="fig1" style="width: 400px;"/>

I had my Saleae running during startup, and it confirmed: Life on the data/address lines! (0-3 shown below in the Saleae software)

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/LifeOnBDAL.png?raw=true" alt="fig1" style="width: 400px;"/>

Could it really be? Was the PDP-11 alive? I started the serial connection....

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/First_ODT.jpeg?raw=true" alt="fig1" style="width: 400px;"/>


ODT!!!!!

The ODT is the [Online Debugging Tool](https://en.wikipedia.org/wiki/On-line_Debugging_Tool) found in later PDP-11 implementations that did not have a front-panel anymore. 

So, the machine was alive, and talking to me! 
Without RAM though, I couldn't load or access any memory locations.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/ODT_noRAM.jpeg?raw=true" alt="fig1" style="width: 400px;"/>

But this was easy to resolve. I had, in the mean time, also acquired a few more RAM boards, including one DEC board that had good documentation so no issues in configuration. Adding that to the backplane, this was now my setup (note the dramatic voltage drop: barely 4.6V on the 5V rail!):

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/Final_setup2.jpg?raw=true" alt="fig1" style="width: 400px;"/>

And, sure enough, now I could type in a simple program that would just call-back the letter 'A' back to the same serial port.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/First_program.jpeg?raw=true" alt="fig1" style="width: 400px;"/>

It all worked!

I made a few small enhancements, one was a pair of flatcables for the serial connection, that way also allowing a second serial connection:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/PortB_working.png?raw=true" alt="fig1" style="width: 400px;"/>

So, I have achieved my goal of building a working, minimal PDP-11 setup. What's next? Well, several things. The most urgent is to have proper cooling - I need to add a fan to force air between te boards. Several components, especially a few power resistors and chips on on the CPU board, are getting really hot. I can't even estimate temperature on other boards since they are inaccessible (the order matters, CPU board going in the first slot, and boards need to be in subsequent slots). If I want to really work with this setup for longer than just 10 minutes at a time or so, I really need to fix that first.

A final shot of all the stuff that I've used for the working solution described here:

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/Final_setup_disassembled_2.jpg?raw=true" alt="fig1" style="width: 400px;"/>

It's nice that I got a first configuration running, but I need a solution for getting these LSI CPU boards running too. It's too unlikely that they are all faulty. And I want to get other stuff connected, in the end running a small operating system like RT-11. Which means disks, tape, etc.. Which is not going to happen as physical devices however much I would like to own a [working RK05](https://www.youtube.com/watch?v=n2UMpINy6dI). For next steps it would be good to have more control and to be able to check what's going on. For that reason I've decided to get myself a [Qbone](http://retrocmp.com/projects/qbone) and [Qprobe](http://retrocmp.com/tools/qprobe) from Joerg Hoppe. Problem: the Qbone requires a four-wide backplane. And since backplanes are by far the hardest to obtain... well in the mean time I managed to get one, so I'm sure more Minimalistic PDP-11 adventures are coming!

### Postscript: "Kratje Kwiebus"

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Kratje/Kwiebus.png?raw=true" alt="fig1" style="width: 500px;"/>

I ended up calling my little minimalistic PDP-11 project "Kratje Kwiebus". A "kratje" is Dutch for a little crate, referring to the rack-shaped backplane. I borrowed this term from Geert Rolf, who started calling my little project "Kratje Qbus". I ended up changing "Qbus" to the similar sounding Dutch term "Kwiebus". "Kwiebus referring to the owner, rather than the architecture of the hardware. I've made so many mistakes along the way, which I couldn't have overcome if it weren't for the help of many (especially Roland, who practically dragged me to the finish line), which sometimes left me feeling foolish - a bit of a "Kwiebus", who, in all his enthusiasm forgets the important stuff before embarking on a quest. But I've learned so much from this project! I had already forgotten it is only one year ago that I really committed myself to diving deeper in old computers. This blog post is to commemorate that anniversary. 

Special thanks to [Henk Gooijen](https://pdp-11.nl), Fred Jan Kraan, Geert Rolf, [Ed Groenenberg](https://www.youtube.com/user/PDP11nl), Joerg Hoppe, Oscar Vermeulen, [Jaak Bartok](http://jaakbartok.be/), Camiel Vandenhoeve, Roland Huisman, and others I'm likely forgetting. While looking for guidance and inspiration, I found a great community of PDP-11 enthusiasts!

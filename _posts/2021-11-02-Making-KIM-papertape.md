### Making KIM-1 papertape ###

There are a number of ways to get data or code into the KIM-1/PAL-1. The one used most often by KIM-1 owners 'back in the day' must have been the audio tape way. The alternative option - connecting from a serial device such as a teletype of 'glass terminal' surely must have been outside of the budget of most KIM users. 

Nowadays, serial connections are easy and cheap. A terminal emulation program on your Windows, Mac, or Linux computer, and a cheap serial RS232-to-USB cable is all that is required. That means that serial input and output are today the preferred option for interfacing with your vintage KIM-1 or [new-kid-on-the-block PAL-1](https://www.tindie.com/products/tkoak/pal-1-a-mos-6502-powered-computer-kit/). 

Serial input, such as loading of a program, traditionally was done from papertape, e.g. on a Teletype ASR33. The teletype would send the information as ASCII-encoded representations of each half-byte, ie. characters 0-F, for hexadecimal. The [KIM-1 manual](http://retro.hansotten.nl/6502-sbc/kim-1-manuals-and-software/kim-1-manuals-and-software/), appendix F, provides a succinct description of the papertape format.

<img src="https://github.com/hjmegens/hjmegens.github.io/blob/master/_posts/figures/Appendix_F.png?raw=true" alt="fig1" style="width: 600px;"/>

Briefly, the information is sent line by line, or rather, record by record, each record starting with a semi-colon (';'), and ending with a carriage return and newline. Every line starts with a number (usually 24 decimal or 0x18 hexadecimal) specifying the number of valid data bytes. Next are four bytes specifying the memory addres for the firts data byte in the record. The first address byte is the high byte, followed by the low byte. Next are 24 databytes, terminated by a checksum of the entire record (2 bytes). The line is terminated by a carriage return and newline, and six 'null characters' or ASCII `00`. The null characters were added to give the KIM-1 a bit of time to check the checksum before going to the next line and finish inserting all ASCII characters to equivalent bytes and store them, before moving on to the next line. In 'virtual papertape' the addition of extra meaningless characters is no longer needed since one can easily control the time to move on to the next character.

The last line of the papertape contains zero data bytes, so first byte in the line is 0 (2 ASCII chars '0'). The next two bytes (4 ASCII characters) indicate the number of lines in the papertape holding valid data, followed, as usual, by a checksum, the last two bytes. The checksum is a litteral sum of all bytes in the record, excluding the starting ';' and the checksum itself. 

For many vintage computer programs written or adapted for the KIM-1, papertape versions are available. 
Since the serial data transfer is by far the easiest from a modern computer to the PAL-1 this now is my preferred way to load software. However, when writing your own assembly code and compiling it to binary, you still need to convert it to papertape. 

There are 6502 assembly compilers that can do this directly for you, but not all. There are also conversion tools that can do the conversion from binary. Why then am I creating my own 'virtual papertaper'? Simply because I think it is a fun learning experience - only when you create a tool like this will you really appreciate all the different aspects of the papertape format and its conversion process. 

### The program ###

My favorite programming language is Python, so, as usual, this is the language I choose whenever I can. I think it is also very useful for the job, as it has many easy commands to work with both bytes and strings. 

Lets first assemble the first example of the First Book of KIM:

```
* = $0200
LDA $10
LDX $11
STA $11
STX $10
BRK
.END
```

We can then compile it with 64tass as follows:
```
./64tass first_of_firstbookofKIM.asm -o first_of_firstbookofKIM.prg
```

The result is as expected but note that 64tass by default will prepend the program code with 2 bytes holding the start address. Not all assemblers do that, but, if they do, it is of course very convenient. 
```
hexdump -C first_of_firstbookofKIM.prg
00000000  00 02 a5 10 a6 11 85 11  86 10 00                 |...........|
0000000b
```

This means that the 'virtual papertaper will have to be able to either read a binary in the CBM hex format (with start address), or just read the raw binary with a starting address provided by the user if otherwise. 

For the example above, with the start address in the binary, conversion to papertape would be as follows:

```
python prg_to_ptp.py -i first_of_firstbookofKIM.prg -o first_of_firstbookof_KIM.ptp
```

The papertape would contain just one line with 9 data bytes:
```
;090200A510A611851186100002A3
;0000010001
```

If the start address is not in the binary, as is for instance the case in the binary for the VUTAPE program from the first book of KIM:

```
hexdump -C VUTAPE.BIN
00000000  d8 a9 7f 8d 41 17 a9 13  85 e0 8d 42 17 20 41 1a  |....A......B. A.|
00000010  46 f9 05 f9 85 f9 8d 40  17 c9 16 d0 e9 20 24 1a  |F......@..... $.|
00000020  c9 2a d0 f5 a9 00 8d e9  17 20 24 1a 20 00 1a d0  |.*....... $. ...|
00000030  d5 a6 e0 e8 e8 e0 15 d0  02 a2 09 86 e0 8e 42 17  |..............B.|
00000040  aa bd e7 1f 8d 40 17 d0  db 00 00 00 00 00 00 00  |.....@..........|
00000050  a0 bf 8c 43 17 a9 16 20  7a 19 d0 f9              |...C... z...|
0000005c
```
There is no start address in the binary, but it can be provided to the papertaper program:

```
python prg_to_ptp.py -i examples_kimtape/VUTAPE.BIN -b -s 0 -o vutape.ptp
```
The `-b` option indicates 'binary', ie. without start address, and the `-s` option then is required to specify the start address, in this case '0'. 

The papertape output looks like this:
```
;180000D8A97F8D4117A91385E08D421720411A46F905F985F98D400B07
;18001817C916D0E920241AC92AD0F5A9008DE91720241A20001AD00993
;180030D5A6E0E8E8E015D002A20986E08E4217AABDE71F8D4017D00D53
;140048DB00000000000000A0BF8C4317A916207A19D0F906B7
;0000040004
```

Loading the papertape to the PAL-1 can be done, with Linux, as follows:

```
ascii-xfr -s -l 100 -c 10 vutape.ptp >/dev/ttyUSB0
```

Of course for those initiated in the KIM-verse the VUTAPE program will be known as a useful tool to diagnose audio tape. But audio tape will be left to (several) further posts. 

The papertaper program can be found in my [PAL1_tools project](https://github.com/hjmegens/PAL1_tools) on Github. 



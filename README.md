# cpm8266

## Z80-CP/M 2.2 emulator running on ESP8266

 ![Screenshot boot](/pic/boot1.png?raw=true "Screenshot of EMON and boot of CP/M")
 
### What

This is my attempt to run CP/M 2.2 for Z80 softwares on an ESP8266.
The CP/M machine have 64K RAM, 15 floppy drives @ 250KB each and an
autobauding serial port as a console device.

Since the ESP8266 only have 96KB of Data RAM and even when runnig the "NONOS-firmware 
from Espressif the free heap, after the WIFI & TCP/IP stacks are
loaded, is less than 48KB which was my minimum goal for CP/M RAM.

Luckily the nosdk8266 project from cnlohr solves the RAM issue. By using the
it I get more than 80KB of heap which is more than enough for
the 64KB RAM in the Emulator. But this unfortunately comes with a cost -
namlely no wifi.  Not really a showstopper for me, but it would have been nice to
be able to just Telnet into the CP/M machine to connect to the emulated
terminal. 

But not all hope is lost for those who want wifi and telnet - I'm currently
patching in an option to compile a version with about 36K RAM, wifi and one
less floppy disk.

 ![Screenshot wifi/telnet](/pic/wifi36k.png?raw=true "Connected over telnet")

The 36K RAM wifi/telnet option works kinda ok, but it only leaves about 5.9K free memory in MBASIC. And it is also too small run Turbo Pascal or the classic Ladder game.

### Installing, Compiling and Running

You will need ESP-Open-SDK installed. If you don't already have it you can
get it at https://github.com/pfalcon/esp-open-sdk. Just follow the
installation instructions there and be prepared for a lengthy (but automated)
process.

I've only setup this for for Debian/Ubuntu but most dists should be fairly
similar.

**Unless you already have git installed you should install it**

```apt-get install git```

**Then install the prerequisites for pfalcon/esp-open-sdk**

```
apt-get install make unrar-free autoconf automake libtool gcc g++ 
apt-get install gperf flex bison texinfo gawk ncurses-dev 
apt-get install libexpat-dev python-dev python python-serial 
apt-get install sed git unzip bash help2man wget bzip2 libtool-bin
```

**Install the esp-open-sdk**

```
git clone --recursive https://github.com/pfalcon/esp-open-sdk.git
cd esp-open-sdk
make
export PATH=~/esp-open-sdk/xtensa-lx106-elf/bin:$PATH
cd ..
```

**Install prerequisites for cpm8266**

```
apt-get install z80asm cpmtools zip vim-common
```

The vim-common is to get the xxd utility that some distros don't install by
default. Thank you @drawkula for reporting this.

**Clone the cpm8266 repo and config your environment**

Instead of setting and exporting these environment variables you could change the settings in the top of the Makefile instead

```
git clone https://github.com/SmallRoomLabs/cpm8266.git
cd cpm8266/code
export ESP8266SDK=~/esp-open-sdk
export ESPTOOL=~/esp-open-sdk/esptool/esptool.py
export ESPPORT=/dev/ttyUSB0
```

**Compile the emulator and all cp/m disks and upload it to the ESP8266**

```make full```

**Connect to the emulator and boot into CP/M**

Run any serial terminal emulator set to 8N1 at any standard speed between 300 and 115200 baud. To run any full screen CP/M programs you should have VT100/ANSI terminal emulation.

Just to get started you can install the "screen" package and use that as a serial terminal.

```apt-get install screen```

And then connect with:

```screen /dev/ttyUSB0 9600```

Press Enter twice to autobaud and boot into the CP/M.

**To compile with the wifi option enabled**

Either do a 
```BUILD=WIFI make full```

or change the BUILD option in the Makefile.

After the full re-compile and upload of all disks you can connect via telnet port 23 (the default telnet port) after the red led (on the NodeMCU board) goes off. The led is lit as long as the wifi has not received the ip address via DHCP.


### Code folder contents:
- *CPM22/* The Z80 assembly sources for CP/M 2.2
- *disks/* Have sub-folders with the files to be put into the simulated disks
- *dist/* Used to hold the file when creating the zipped binary distributions
- *espbin/* SDK bin-files from Espresif to be uploaded in high flash 
- *include/* .h files from NoSDK
- *ld/* Linker scripts
- *nosdk/* Modified files from the NoSDK repo
- *z80/* Modified files from the Z80 emulator repo 



### Acknowledgements and thanks
I'm standing on the shoulders of a lot of really smart people here. Without
their contributions I wouldn't even know where to start.

Paul Sokolovsky _@pfalcon_ made the Esp-Open-SDK installer/setup. https://github.com/pfalcon

Lin Ke-Fong _@anotherlin_ made the Z80 Emulator. https://github.com/anotherlin

Charles Lohr _@CNLohr_ created the NoSDK for the ESP8266. https://github.com/cnlohr

Tim Olmstead (RIP) for managing to free the CP/M sources from their owners http://www.cpm.z80.de/ 

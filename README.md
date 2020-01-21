# c201-maskrom-mode


Overview of available tools: https://roc-rk3328-cc.readthedocs.io/en/latest/flash_emmc.html

Open source maskrom mode writer https://github.com/rockchip-linux/rkdeveloptool

cable:  D- to D-, D+ to D+ and gnd to gnd
*DO NOT CONNECT VCC (5v) to VCC, DOING THIS WILL BRICK YOUR DEVICE*

Steps: 
Backup the coreboot/depthcharge rom
Wipe the coreboot/depthcharge rom
connect usbA to usbA, not sure which of the two ports but likely the top one (must turn on laptop with cable plugged in, no hotplug most likely) 
reboot, should enter mask rom mode
see if it shows up in lsusb
try to flash the rom with rkdevelopmentool

Note if it works without battery



First Test:
setup as above
used top c201 usb ports
needs either battery or ac power attached

shows up in lsusb!
```
Bus 001 Device 012: ID 2207:320a Fuzhou Rockchip Electronics Company RK3288 in Mask ROM mode
```
and `rkdevelopmentool` sees it!

```
$ ./rkdeveloptool ld
DevNo=1 Vid=0x2207,Pid=0x320a,LocationID=102    Maskrom
```

but none of the other read, chipinfo commands, write commands etc worked. After running the command, no output was given but the tool didn't exit. 


loaded up the boot loader found on the xda fourms:

```
$ rkdeveloptool db For_RK32xx_devices/RK32xxLoader\(L\)_uboot_V2.15_replace_ddr.bin
Downloading bootloader succeeded.
```

and got some serial output on the c201:

```
DDR Version 1.00 20141007
In
Channel a: LPDDR3 200MHz
MR0=0x58
MR1=0x58
MR2=0x58
MR3=0x58
MR4=0x2
MR5=0x1
MR6=0x5
MR7=0x0
MR8=0x1F
MR9=0x1F
MR10=0x1F
MR11=0x1F
MR12=0x1F
MR13=0x1F
MR14=0x1F
MR15=0x1F
MR16=0x1F
Bus Width=32 Col=10 Bank=8 Row=15 CS=2 Die Bus-Width=32 Size=2048MB
Channel b: LPDDR3 200MHz
MR0=0x58
MR1=0x58
MR2=0x58
MR3=0x58
MR4=0x2
MR5=0x1
MR6=0x5
MR7=0x0
MR8=0x1F
MR9=0x1F
MR10=0x1F
MR11=0x1F
MR12=0x1F
MR13=0x1F
MR14=0x1F
MR15=0x1F
MR16=0x1F
Bus Width=32 Col=10 Bank=8 Row=15 CS=2 Die Bus-Width=32 Size=2048MB
Memory OK
Memory OK
OUT
serial_init 1
ChipType = 8
...FlashInit enter...
FtlMallocOffset = 8040 8000
FtlMallocOffset = 10040 8000
FtlMallocOffset = 11040 1000
FtlMallocOffset = 19040 8000
FtlMallocOffset = 1a040 1000
1:0 0 7f7f05 22
...NandcInit enter...
0:1200 0 7f7f05 22
gNandcVer = 6
 SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1777
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1777
 mmc Ext_csd, ret=0 ,
 Ext[226]=20, bootSize=2000, 
                 Ext[215]=1, Ext[214]=d5, Ext[213]=c0, Ext[212]=0,cap =1d5c000 
SdmmcInit=2 0
BootCapSize=2000
UserCapSize=1d5c000
FwPartOffset=0 , 0
UsbHook 594408
powerOn 595790
```


After that, a test read worked!

```
./rkdeveloptool rl 0x0 0x400000  test
Read LBA to file (100%)
```


Found there were specific rk3288 images on xda as well, Including a uboot image (thanks @swiftgeek)

reset the device
```
$ rkdeveloptool rd
Reset Device OK.

```

loaded up the downloadboot loader:

```
$ rkdeveloptool db For_RK3288_devices/'RK3288Loader(L)_V2.17.bin'
Downloading bootloader succeeded.
```

and got some more serial output:
```

DDR Version 1.00 20141007
In
SRX
Channel a: LPDDR3 200MHz
MR0=0x58
MR1=0x58
MR2=0x58
MR3=0x58
MR4=0x2
MR5=0x1
MR6=0x5
MR7=0x0
MR8=0x1F
MR9=0x1F
MR10=0x1F
MR11=0x1F
MR12=0x1F
MR13=0x1F
MR14=0x1F
MR15=0x1F
MR16=0x1F
Bus Width=32 Col=10 Bank=8 Row=15 CS=2 Die Bus-Width=32 Size=2048MB
Channel b: LPDDR3 200MHz
MR0=0x58
MR1=0x58
MR2=0x58
MR3=0x58
MR4=0x2
MR5=0x1
MR6=0x5
MR7=0x0
MR8=0x1F
MR9=0x1F
MR10=0x1F
MR11=0x1F
MR12=0x1F
MR13=0x1F
MR14=0x1F
MR15=0x1F
MR16=0x1F
Bus Width=32 Col=10 Bank=8 Row=15 CS=2 Die Bus-Width=32 Size=2048MB
OUT
serial_init 1
ChipType = 8
SDC_BusRequest:  CMD=8 DATA BUSY  1609
SDC_BusRequest:  CMD=8 DATA BUSY  1609
SDC_BusRequest:  CMD=8 DATA BUSY  1609
SdmmcInit=0 400
...FlashInit enter...
FtlMallocOffset = 8040 8000
FtlMallocOffset = 10040 8000
FtlMallocOffset = 11040 1000
FtlMallocOffset = 19040 8000
FtlMallocOffset = 1a040 1000
1:0 0 7f7f05 22
...NandcInit enter...
0:1200 0 7f7f05 22
gNandcVer = 6
 SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=8  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=5  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1778
  SDC_BusRequest:  CMD=55  SDC_RESP_TIMEOUT 1778
 mmc Ext_csd, ret=0 ,
 Ext[226]=20, bootSize=2000, 
                 Ext[215]=1, Ext[214]=d5, Ext[213]=c0, Ext[212]=0,cap =1d5c000 
SdmmcInit=2 0
BootCapSize=2000
UserCapSize=1d5c000
FwPartOffset=2000 , 0
UsbHook 660880
powerOn 661898
```


I tried the other images:
```
RK3288Loader_uboot_Apr212014_134842.bin
RK3288Loader_uboot_Apr182014_155036.bin
RK3288Loader_uboot_V2.17.02.bin
```

and got the same serial output as above


rid, rfi, rci reads
first I loaded up RK3288LoaderL_V2.17.bin

```
$ rkdeveloptool rfi
Flash Info:
        Manufacturer: SAMSUNG, value=00
        Flash Size: 15032 MB
        Block Size: 512 KB
        Page Size: 2 KB
        ECC Bits: 0
        Access Time: 40
        Flash CS: Flash<0>

$ rkdeveloptool rci
Chip Info:  41 30 32 33 20 0 0 0 0 0 0 2 0 0 0 0

$ rkdeveloptool rid
Flash ID: 45 4D 4D 43 20

```

tried rcb as well but it failed:
```
$ rkdeveloptool rcb
Read capability Fail!

```


Ram part numbers:
2 of K4EBE304EB-EGCF

16GB Flash part number:
H26M52103FMA
710Y

25xx bios rom chip is:
gigadevices 25q32csig

# Uboot building notes
```
sudo apt install swig gcc-arm-linux-gnueabi rkflashtool
CROSS_COMPILE=arm-linux-gnueabi- make O=chromebook_minnie chromebook_minnie_defconfig all
```
put together the pieces and flashing the image
```
./chromebook_minnie/tools/mkimage -n rk3288 -T rkimage -d chromebook_minnie/spl/u-boot-spl.bin && \
   cat out | openssl rc4 -K 7c4e0304550509072d2c7b38170d1711 | rkflashtool l

```
only get:
```
No serial driver found
resetting ...
```


# c201-maskrom-mode


Overview of available tools: https://roc-rk3328-cc.readthedocs.io/en/latest/flash_emmc.html

Open source maskrom mode writer https://github.com/rockchip-linux/rkdeveloptool

cable:  D- to D-, D+ to D+ and gnd to gnd

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
seems to need the power plugged in?

shows up in lsusb!
```
Bus 001 Device 012: ID 2207:320a Fuzhou Rockchip Electronics Company RK3288 in Mask ROM mode
```
and `rkdevelopmentool` sees it!

```
$ ./rkdeveloptool ld
DevNo=1 Vid=0x2207,Pid=0x320a,LocationID=102    Maskrom
```


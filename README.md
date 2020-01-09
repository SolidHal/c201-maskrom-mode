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

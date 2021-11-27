# How to configure Raspberry Pi 3B+ to USB boot

## Hardware setup

- [Raspberry Pi 3B+](https://www.raspberrypi.com/products/raspberry-pi-3-model-b-plus/)
- [M.2 DTE/DCE expansion hat for RPI](https://www.conrad.se/p/m2-dtedce-expansionskort-foer-raspberry-pi-1487097)

##  Format USB mass storage disk before use

För att hitta vilken diskar som är inkopplade:
I connected my USB mass storage device to my laptop and used this command to determine what Linux disk device it is attached to:
```bash
sudo fdisk -l
```
For me it was /dev/sda. ***Make sure it is the right disk before you proceed!***

The next step was to remove all partitions on that disk (effectively formatting it)
```bash
sudo fdisk /dev/sda
# Enter "d 1" to delete partition 1.
# Enter "d 2" to delete partition 2 and so on..
# Enter "w" to save changes.
```

## Enable booting via USB mass storage

1. Install your image on the disk:
```bash
sudo dd if=rasbian.img of=/dev/sda bs=4M
```
2. Add the `program_usb_boot_mode=1` to /boot/config.txt:
either by manually or running:
```bash
echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
```
for me the boot partition was installed as /dev/sda1.

3. Reboot the rpi and check that the EEPROM bits are set by running:
```bash
vcgencmd otp_dump | grep 17
# 17:1020000a - means that the bit is not set
# 17:3020000a - means that the bit is set!
# if 0x3020000a is not shown, the rpi will not to be able to boot via USB
```
If the bits are set correctly the rpi should be able to boot from the USB mass storage.

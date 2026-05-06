Here is all the needed files and guide to make a bootable FreeDOS drive to flash Supermicro X9DRH-7F on-board LSI SAS2208 RAID controller to LSI SAS2308 IT HBA.

All the info I got from [this video](//www.youtube.com/watch?v=CDatT8fn9KQ) from [Art of Server](https://www.youtube.com/channel/UCKHE9DEep52XlmwLbZUKvyw) youtube channel (subscribe and hit the 🔔 button).

Follow the instructions below to make a bootable FreeDOS drive with all the files needed for flashing then follow the instructions from the video above to actually flash your controller.

SMC2308.SBR has been generated using config from the video above and it's also available in the repo. Versions of tools and firmware are the same as used in the video.
## Disclaimer
**I'm not responsible for any damage caused using files and/or instructions from this repo. Use it at your own risk!**
## Instrucitons of how to make a bootable FreeDOS drive
1. Clone this repo:
```shell
git clone https://github.com/maximko/SAS2208-to-SAS2308-IT-X9DRH-7F
```
2. Download and unzip FreeDOS usb image:
```shell
wget https://www.ibiblio.org/pub/micro/pc-stuff/freedos/files/distributions/1.3/official/FD13-FullUSB.zip && unzip FD13-FullUSB.zip
```
3. Write it to the usb drive (replace /dev/sdX with your drive):
```shell
dd bs=1M status=progress if=FD13FULL.img of=/dev/sdX
```
4. Mount the drive:
```shell
mkdir /mnt/lsi-SAS2208-flash && mount /dev/sdX1 /mnt/lsi-SAS2208-flash
```
5. Copy all the files from this repo to the drive and unmount it:
```shell
cp SAS2208-to-SAS2308-IT-X9DRH-7F/files/* /mnt/lsi-SAS2208-flash && umount /mnt/lsi-SAS2208-flash
```
6. Follow the steps from [this video](//www.youtube.com/watch?v=CDatT8fn9KQ) to flash your controller. Linux x86_64 binary of sas2flash is also available in the repo. The steps in short:
```shell
# Under linux Backup yous SAS Address (you can use megacli in dos too
$ sudo storcli /c0 show all | grep -i "sas address"

# Under FreeDOS, backup card config and prepare to flash
> megarec -readsbr 0 2208_backup.sbr
> megarec -readspd 0 2208_backup.spd
> megarec -writesbr 0 SMC2308.SBR
> megarec -cleanflash 0

# Find the USB filesystem (usually fs0: or fs1:)
> map

# Reboot to UEFI from same flash drive (use dir to see if the fs is correct)
> fs1:

# Verify the wiped card is detected
> sas2flash.efi -list

# Flash firmware and option ROM
> sas2flash.efi -c 0 -o -f 2308T207.ROM -b mptsas2.rom

# restore SAS address (you may need restart, can be any address start with 500 + random hex numbers)
> sas2flash.efi -c 0 -o -sasadd 500304801a3xxxxx

# use same commands but your backup SBR + the official Broadcom ROM for your card to restore RAID
```

## Everything else
Feel free to open an issue if you need any help. Star and follow are appreciated 😉

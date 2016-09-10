# Dialog Robot

## Demo

{% youtube %}https://www.youtube.com/watch?v=Smp5kGfYSCE {% endyoutube %}

## Prerequisites

1. USB sound card, such as https://www.amazon.com/External-Adapter-Desktop-Notebook-Laptop/dp/B001MW92AE

2. USB OTG cable
3. Speaker 
4. Microphone
5. Micro SD card


## Let's get started 


### Congigure the USB sound card.

First log in to 7688 and type following commands in tty.

Install usb audio driver.

```sh
opkg update
opkg install kmod-usb-audio
```

Now plug in your usb sound card to 7688 with a usb OTG cable.

Launch alsamixer to tune the gain of the usb sound card.

```sh
alsamixer
```
Remember to press <F6> to select USB sound card 

![](pic/alsamixer.png)


### Extend root file system to SD card.

Since the original space on 7688 is too small to complete this project, 
we need to extend the system to external device.

List orginal space on-board flash.

```sh
df -h
```
![](pic/original-device-space.png)


Install the required packages.

```sh
opkg update
opkg install block-mount kmod-fs-ext4 kmod-usb-storage-extras e2fsprogs fdisk
```

Insert the SD card and format it to ext4 file system.

Note that if mmcblk0 is not found, reboot may fix it.

```sh
reboot
```

And if the system alerts that /dev/mmcblk0 is mounted, you should unmount it first.

```sh
umount /dev/mmcblk0
```

Format the sd card, press y to to confirm the process to remove all data on it.

Please be patient since the process may take a few minutes if your sd card has large space.

```sh
mkfs.ext4 /dev/mmcblk0
```

Duplicate the root file system to SD card.

```sh
mount /dev/mmcblk0 /mnt
tar -C /overlay -cvf - . | tar -C /mnt -xf -
umount /mnt
```

Congiure the fstab.

Generate file system table, and then change the target option to '/overlay', 
and the enabled option to '1'.

```sh
block detect | 
sed $'s/enabled[[:blank:]]\'0\'/enabled \'1\'/g' |
sed 's%/mnt/mmcblk0%/overlay%g' > /etc/config/fstab
```


Reboot and check the new space.

```sh
reboot
df -h
```

![](pic/new-device-space.png)


## Deploy the app on 7688

First, download the app folder to 7688. Using scp or SD card is recommended.

Second, plug in USB sound card on 7688 USB port, and then connect microphone and speaker.

Finally, log in to 7688 and clone this repo,
and **replace the app/my-stt-credentials with your credentials**,
then launch the application in app folder.

```sh
git clone https://github.com/YuanYouYuan/Bluemix-tutorial.git
cd Bluemix-tutorial/7688/app
node app.js
```




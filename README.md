# WINAMP_LINUX_MILKV_256M

Goal is to create portable player with winamp like interface on milkv_duo 256M RISCV board running linux on SDK v2.

WIP NOT STABLE YET! AT OWN RISC

Steps:

x) lets grab SDK V2 from https://github.com/milkv-duo/duo-buildroot-sdk-v2/releases/ note that you need riscv one not arm one.

x) get a ubuntu archive from https://github.com/bassusteur/milkv-duo-ubuntu and download release rootfs

o)Burn them in

x) using dd or balenaetcher burn image sdk to sd card

x) resize partition 2 on sdcard to fill whole card but do not wipe it

x) copy ubuntu root fs tar archive to /root/ folder

x optional) boot image and set time via date -s '2025-06-05 00:24:56' to current time/date

x) extract ubuntu rootfs archive to ./extract/ dir

x) perform cp -R ./extract/bin/* /bin/&&cp -R ./extract/etc/* /etc/&&cp -R ./extract/lib/* /lib/&&cp -R ./extract/usr/* /usr/ &&cp -R ./extract/var/* /var/ 


x optional) to add socks proxy run echo 'Acquire::socks::proxy "socks5h://localhost:1080/";\nAcquire::http::proxy "socks5h://localhost:1080/";\nAcquire::https::proxy "socks5h://localhost:1080/";' >  /etc/apt/apt.conf.d/22-socks-proxy

x optional) relogin from main linux while runing microsocks via ssh -R 1080:localhost:1080  root@192.168.42.1

x optional) test socks via nc -v -x localhost:1080 google.com 80

x) perform apt update&&apt upgrade

TODO:

Add Display

Add sensor or button input

Add X11

Add display driver

Add input driver

Add QMMP

Add bluetooth(+wifi?) connection via USB or other bus (usb adapter or esp 8266?)


additinal links:

https://milkv.io/docs/duo/getting-started/download

# WINAMP_LINUX_MILKV_256M

Goal is to create portable player with winamp like interface on milkv_duo 256M RISCV board running linux on SDK v2.

WIP NOT STABLE YET! AT OWN RISC

Steps:

--- OS SYSTEM

x) lets grab SDK V2 from https://github.com/milkv-duo/duo-buildroot-sdk-v2/releases/ note that you need riscv one not arm one.

x) get a ubuntu archive from https://github.com/bassusteur/milkv-duo-ubuntu and download release rootfs

o)Burn them in

x) using dd or balenaetcher burn image sdk to sd card

x) resize partition 2 on sdcard to fill whole card but do not wipe it

x) copy ubuntu root fs tar archive to /root/ folder

x optional) boot image and set time via date -s '2025-06-05 00:24:56' to current time/date

x) extract ubuntu rootfs archive to ./extract/ dir

x) perform 

cp -Rndf ./extract/bin/* /bin/

cp -Rdnf ./extract/etc/* /etc/ 

cp -Rdnf ./extract/lib/* /lib/ 

cp -Rdnf ./extract/usr/* /usr/ 

cp -Rndf ./extract/var/* /var/ 

 awk -F: -vOFS=":" '{if(!($1 in gname || $3 in gnumber)){print $1,"x",$3,$4};gname[$1]=1;gnumber[$3]=1
}' /etc/group ./extract/etc/group > /etc/group.new&&cp /etc/group /etc/group.old&&cp /etc/group.new /etc/group

awk '  BEGIN {     OFS = ":"   }   { if (index($1, ":") != 0) {       split($1,a,":") ;      username = a[1]  ;   } else {       username = $1     }    if ( ( $1 in merged_users ) ) {
    print "Duplicate user: " username    } else {       merged_users[ $1 ]= $0  ;       print $0     }   } ' /etc/passwd ./extract/etc/passwd > /etc/passwd.merge&&cp /etc/passwd.merge /etc/passwd

rm -R /var/cache&&mkdir /var/cache&&chmod -R 777 /var/cache&&

if system is unbootable replace files in this dirs from SDK cp -R is replacing files! cp -Rn not.


x optional) to add socks proxy run echo 'Acquire::socks::proxy "socks5h://localhost:1080/";\nAcquire::http::proxy "socks5h://localhost:1080/";\nAcquire::https::proxy "socks5h://localhost:1080/";' >  /etc/apt/apt.conf.d/22-socks-proxy

x optional) relogin from main linux while runing microsocks via ssh -R 1080:localhost:1080  root@192.168.42.1

x optional) test socks via nc -v -x localhost:1080 google.com 80

x optional) stuff to fix system

hostname milkv-duo

apt update&&apt --reinstall install apt coreutils dpkg findutils sysvinit-utils ubuntu-minimal apt-utils whohas man init-system-helpers systemd systemd-sysv  systemd systemd-sysv init libpam-systemd  sysvinit-utils systemd-sysv sysfsutils cgroupfs-mount cgroup-tools autofs rsyslog  dbus  udev  systemd-hwe-hwdb libpam-systemd:riscv64 networkd-dispatcher ubuntu-minimal ca-certificates systemd netplan.io systemd-timesyncd systemd-sysv libnss-systemd:riscv64

sudo apt remove --allow-remove-essential systemd systemd-sysv init

ln -s /bin/busybox /sbin/init 

apt --reinstall install ifupdown





 

x) perform apt update&&apt upgrade

x) unfortunetly SPIDEV is configured incorrectly so we need to recompile sdk v2 with this options

sudo apt install -y pkg-config build-essential ninja-build automake autoconf libtool wget curl git gcc libssl-dev bc squashfs-tools android-sdk-libsparse-utils jq scons parallel python3-distlib  tree python3-dev python3-pip device-tree-compiler ssh cpio fakeroot libncurses-dev flex bison libncurses5-dev genext2fs rsync unzip dosfstools mtools tcl openssh-client cmake expect python-is-python3 python3-jinja2 xxd

build SLIB

 git clone https://github.com/SLIBIO/SLib.git&&cd SLib&&./setup-path&&cd .. #python3-distutils replaced with python3-distlib 

git clone https://github.com/milkv-duo/duo-buildroot-sdk-v2.git --depth=1&&wget https://github.com/milkv-duo/duo-buildroot-sdk-v2/releases/download/dl/dl.tar
&&tar xvf ./dl.tar -C ./duo-buildroot-sdk-v2/buildroot/&&cd duo-buildroot-sdk-v2/&&./build.sh milkv-duo256m-musl-riscv64-sd

duo-buildroot-sdk-v2/cvi_mpi/modules/audio/audio.mk
from
USE_ALSA = no
USE_TINYALSA = yes
to
USE_ALSA = yes
USE_TINYALSA = no

fix compilation 
cvi_mpi/modules/audio/Makefile
PREBUILD_LIBDIR = ./prebuilt/$(TARGET_MACHINE) 

error with alsa 
*** No rule to make target 'src/cvi_audio_interface.c', needed by '/mnt/512G/builds/duo-buildroot-sdk-v2/cvi_mpi/lib/libcvi_audio.a'.  Stop.

build/env_setup_milkv.sh:519
function build_pqtool_server()
{(
  return;


  DTS compile
preprocess

   dtcpp -I ./u-boot-2021.10/arch/riscv/dts/ -I ./u-boot-2021.10/include/ ./build/boards/cv181x/sg2002_milkv_duo256m_musl_riscv64_sd/dts_riscv/sg2002_milkv_d
uo256m_musl_riscv64_sd.dts  ./build/boards/cv181x/sg2002_milkv_duo256m_musl_riscv64_sd/dts_riscv/sg2002_milkv_duo256m_musl_riscv64_sd.dts.preprocessed

enable ubuntu systemd

/duo-buildroot-sdk-v2/build/boards/cv181x/sg2000_milkv_duos_musl_riscv64_sd/sg2000_milkv_duos_musl_riscv64_sd_defconfig add at end

CONFIG_CGROUPS=y
CONFIG_CGROUP_FREEZER=y
CONFIG_CGROUP_PIDS=y
CONFIG_CGROUP_DEVICE=y
CONFIG_CPUSETS=y
CONFIG_PROC_PID_CPUSET=y
CONFIG_CGROUP_CPUACCT=y
CONFIG_PAGE_COUNTER=y
CONFIG_MEMCG=y
CONFIG_CGROUP_SCHED=y
CONFIG_NAMESPACES=y
CONFIG_OVERLAY_FS=y
CONFIG_AUTOFS4_FS=y
CONFIG_SIGNALFD=y
CONFIG_TIMERFD=y
CONFIG_EPOLL=y
CONFIG_IPV6=y
CONFIG_FANOTIFY
CONFIG_ZSMALLOC=y
CONFIG_ZRAM=y



---- DISPLAY

*milkV

pin 36 VCC 3.3V out

pin 38 GND 

pin 9  GNUM 375 SCL SPI2_SCK O SD1_CLK

pin 10 GNUM 374 DC SPI2_SDO I/O SD1_CMD 

pin 11 GNUM 373 SDA SPI2_SDI I SD1_D0?

pin 12 GNUM 370 CS SPI2_CS_X O SD1_D3 1

pin porte 4 GWR RST

pin RST 21? 

pin DC 22???

*GC9A01 1.28 240x240  16-bit color 4 WIRE SPI  round TFT 

pins VCC,GND,SCL(SCK (Serial Clock)),SDA(Serial Data),DC(data/command),CS(chip select),RST(reset?)


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

https://github.com/milkv-duo/duo-examples

https://github.com/waveshareteam/LCD-show

https://github.com/juliannojungle/gc9a01-overlay

https://github.com/raspberrypi/linux/tree/rpi-5.15.y/arch/arm/boot/dts/overlays



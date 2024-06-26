# KarliBTS_LXD
# CREATE CONTAINERS FOR KarliBTS
# Hardware schematic
[demo1](https://www.youtube.com/watch?v=CebldhIqmDs) </br>
[demos2](https://www.youtube.com/watch?v=_nGVeG_76W8&pp=ygUJa2FybGkgZ3Nt) </br>
Hardware setup 1 : Need battery and not programmable with arduino  

[serial_cable](https://osmocom.org/projects/baseband/wiki/Serial_Cable)    [smartspate](https://www.smartspate.com/how-to-create-2g-network-at-your-own-home/)   [sudonull](https://sudonull.com/post/69473-Launching-a-GSM-network-at-home-Pentestit-Blog)
<p align="center">
  <img src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/usb_motorola.jpg">
</p>

<p align="center">
  <img width="600" height="400" src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/usb_cable_final.png">
</p>


Hardware setup 2 : No need battery and programmable with arduino  
<p align="center">
  <img src="https://github.com/SitrakaResearchAndPOC/GSM_IMSICATCHER_HALFMITM_SPOOFING-SMS-WITH-PHYSICAL-MS/blob/main/USB_TTL.jpg">
</p>



## installation with LXC  using ubuntu
```
apt update
```
```
apt-get install lxd lxd-client
```


## installation with LXC  using DragonOS
```
apt update
```
```
apt-get install snapd  
```
```
snap install lxd  
```

```
snap install lxd-client  
```

## initialisation of LXD
```
lxd init  
```
For questions please follow the default option, an image illustration is at [image](https://github.com/SitrakaResearchAndPOC/QCSuper/blob/main/screen.jpg) 
  
User need to be in group lxd :
```
sudo usermod -a G lxd $USER  
```
or  
```
sudo /usr/sbin/usermod lxd $USER  
```

$PATH need to contains /usr/local/bin, verify with :  
```
echo $PATH  
```

if not, setup this, or add this in your .bashrc or .zshrc or ...  

```
export PATH=$PATH:/usr/local/bin  
```


## creating debian on lxc
```
lxc launch images:debian/10 KarliBTS
```
```
lxc exec KarliBTS -- bash
```
## editing source.list
```
vi /etc/apt/source.list
```
```
#

# deb cdrom:[Debian GNU/Linux 10.13.0 _Buster_ - Official amd64 DVD Binary-1 20220910-18:04]/ buster contrib main

#deb cdrom:[Debian GNU/Linux 10.13.0 _Buster_ - Official amd64 DVD Binary-1 20220910-18:04]/ buster contrib main

# Line commented out by installer because it failed to verify:
#deb http://security.debian.org/debian-security buster/updates main contrib
# Line commented out by installer because it failed to verify:
#deb-src http://security.debian.org/debian-security buster/updates main contrib

# buster-updates, previously known as 'volatile'
# A network mirror was not selected during install.  The following entries
# are provided as examples, but you should amend them as appropriate
# for your mirror of choice.
#
 deb http://deb.debian.org/debian/ buster main contrib
 deb-src http://deb.debian.org/debian/ buster main contrib
```
Tape ctrl+x  
and :w + enter  
and :q + enter  

## installing depencies 
```
apt update
```
```
sudo apt-get install libtool shtool automake dahdi-source libssl-dev sqlite3 libsqlite3-dev libsctp-dev libfftw3-dev libfftw3-3 autoconf libsctp-dev libgnutls28-dev libcurl4-gnutls-dev git-core pkg-config make gcc gcc-arm-none-eabi doxygen libtalloc-dev libpcsclite-dev libusb-1.0-0-dev
```
```
sudo apt install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool g++ git inetutils-tools libboost-all-dev libncurses5 libncurses5-dev libusb-1.0-0 libusb-1.0-0-dev libusb-dev python3-dev python3-mako python3-numpy python3-requests python3-scipy python3-setuptools python3-ruamel.yaml
```
```
apt-get install nano wget
```
## creating and installing osmocom
```
mkdir osmocom
```
```
cd osmocom
```
installing libosomcore
```
git clone https://git.osmocom.org/libosmocore.git
```
```
cd libosmocore/
```
```
git checkout cf70aa0c40c574c32b832454f725cc37459c5d8d
```
```
autoreconf -i
```
```
./configure
```
```
make -j4
```
```
make install
```
```
ldconfig -i
```
```
cd .. 
```

  
installing osmocom-bb
```
git clone https://git.osmocom.org/osmocom-bb.git
```
```
cd osmocom-bb/
```
```
git checkout 4f677e6ba8434dab376495cd996d140548fa6e93
```
```
cd src
```
```
nano target/firmware/Makefile
```
"#uncomment CFLAGS += -DCONFIG_TX_ENABLE in the file target/firmware/Makefile"  
#ctrl+o return ctrl+x  
```
tail -f target/firmware/Makefile
```
```
make -j4 -e CROSS_TOOL_PREFIX=arm-none-eabi-
```
```
cd ../..
```

  
installing libosmo-dsp
```
git clone https://git.osmocom.org/libosmo-dsp.git
```
```
cd libosmo-dsp/
```
```
git checkout 551b9752bcd5d3d21bb2df0736b1801bda3d0d10
```  
```  
autoreconf -i
```
```
./configure
```
```
make -j4
```
```
make install
```
```
ldconfig -i
```
```
cd ..
```

installing trx
```
git clone https://git.osmocom.org/osmocom-bb.git -b fixeria/trx trx
```
```
cd trx/src/
```
```
git checkout 620fe497efa492feff4550e336cc3f8167715936
```
"#uncomment CFLAGS += -DCONFIG_TX_ENABLE in the file target/firmware/Makefile"  
#ctrl+o return ctrl+x  
```
nano target/firmware/Makefile
```
```
tail -f target/firmware/Makefile
```
```
make -j4 HOST_layer23_CONFARGS=--enable-transceiver -e CROSS_TOOL_PREFIX=arm-none-eabi-
```
```
cd ../..
```  

  
installing dependencies before libosmo-abis
``` 
apt-get install libortp-dev
```
```
apt-get install libtool shtool automake dahdi-source libssl-dev sqlite3 libsqlite3-dev libsctp-dev libfftw3-dev libfftw3-3 autoconf libsctp-dev libgnutls28-dev libcurl4-gnutls-dev git-core pkg-config make doxygen libtalloc-dev libpcsclite-dev libusb-1.0-0-dev
```
installing libosmo-abis
```
git clone https://git.osmocom.org/libosmo-abis.git
```
```
cd libosmo-abis/
```
```
git checkout 39dffb6c29a8d78ba8527aa4ccc13f34d1c3b319
```
```
autoreconf -i
```
```
./configure
```
```
make -j4
```
```
make install
```
```
ldconfig
```
```
cd ..
```
installing libosmo-netif
```
git clone https://git.osmocom.org/libosmo-netif.git
```
```
cd libosmo-netif/
```
```
git checkout 09c71b04f5a8d82515d0d4d541b8368b585dbd31
```
```
autoreconf -i
```
```
./configure
```
```
make -j4
```
```
make install
```
```
ldconfig
```
```
cd ..
```
installing dependecies for openbsc
```
sudo apt install build-essential gcc g++ make automake autoconf libtool pkg-config libtalloc-dev libpcsclite-dev libortp-dev libsctp-dev libssl-dev libdbi-dev libdbd-sqlite3 libsqlite3-dev libpcap-dev libc-ares-dev libgnutls28-dev libsctp-dev sqlite3 libsofia-sip-ua-glib-dev libusb-1.0-0-dev libfftw3-dev libgsm1-dev
```
```
sudo apt install autoconf automake build-essential ccache cmake cpufrequtils doxygen ethtool g++ git inetutils-tools libboost-all-dev libncurses5 libncurses5-dev libusb-1.0-0 libusb-1.0-0-dev libusb-dev python3-dev python3-mako python3-numpy python3-requests python3-scipy python3-setuptools python3-ruamel.yaml
```
```
sudo apt install asterisk telnet python3-pip
```

installing open-bsc
```
git clone https://git.osmocom.org/openbsc.git
```
```
cd openbsc/openbsc/
```
```
git checkout d2550da76f9974bb1957f74c5d3eb75fdae923d9
```
```
autoreconf -i
```
```
./configure
```
```
make -j4
```
```
make install
```
```
ldconfig
```
```
cd ../..
```
installing osmo-bts
```
git clone https://git.osmocom.org/osmo-bts.git
```
```
cd osmo-bts/
```
```
git checkout 59e7773055335a12d749faf84d88a8ed9fa0f201
```
```
autoreconf -i
```
```
./configure --enable-trx
```
```
make -j4
```
```
make install
```
```
ldconfig
```
```
cd ../..
```
Copying osmocon
```
cp osmocom/trx/src/host/osmocon/osmocon ../root/
```
```
chmod +x osmocon
```
Copying trx
```
cp osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin ../root/
```

## BEFORE LAUNCHING BTS, PREPARING ALL CONFIGS
* config file of osmo-bts
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/CalypsoBTS_Debian/main/osmo-bts.cfg
``` 
* config file of open-bsc
``` 
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/CalypsoBTS_Debian/main/open-bsc.cfg
``` 
* config  hlr.sqlite3
```
touch hlr.sqlite3
```
```
exit
```

# ADDING DEVICES ON LXC
Plug usb ttl for motorola phone
## Finding all devices
```
dmesg | grep ttyUSB*
```
## Adding devices on LXC
* For on Phone (SMS only)
```
lxc config device add KarliBTS ttyUSB0 unix-char path=/dev/ttyUSB0
```  
* For two Phones (SMS and Call)
```
lxc config device add KarliBTS ttyUSB0 unix-char path=/dev/ttyUSB0
```
```
lxc config device add KarliBTS ttyUSB1 unix-char path=/dev/ttyUSB1
```
## Setting privileges
```
lxc config set KarliBTS security.privileged=true
```

# LAUNCHING OSMOCOM ONE PHONE (SMS ONLY)
## Terminal 1 
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB0 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin
```
## or Terminal 1
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB0 -c trx.highram.bin
```
Tape ctrl+shift+T

## Find ARFCN
Tape `*#*#4636#*#*` and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as ARFCN  

## Terminal 2
```
lxc exec KarliBTS -- osmocom/trx/src/host/layer23/src/transceiver/transceiver -a ARFCN 
```
Tape ctrl+shift+T
## Terminal 3
```
lxc exec KarliBTS -- osmo-nitb -c open-bsc.cfg -l hlr.sqlite3 -P -C --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Tape ctrl+shift+T
## Terminal 4
```
lxc exec KarliBTS -- osmo-bts-trx -c osmo-bts.cfg --debug DRSL:DOML:DLAPDM 
```


# LAUNCHING OSMOCOM TWO PHONES (SMS AND CALL)
## Terminal 1 
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB0 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin
```
## or Terminal 1
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB0 -c trx.highram.bin
```
Tape ctrl+shift+T
## Terminal 2
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB1 -s /tmp/osmocom_l2.2 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin 
```
## or Terminal 2
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB1 -s /tmp/osmocom_l2.2 -c trx.highram.bin 
```
Tape ctrl+shift+T

## Find ARFCN
Tape `*#*#4636#*#*` and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as ARFCN  

## Terminal 3
```
lxc exec KarliBTS -- osmocom/trx/src/host/layer23/src/transceiver/transceiver -a ARFCN -2
```
Tape ctrl+shift+T
## Terminal 4
```
lxc exec KarliBTS -- osmo-nitb -c open-bsc.cfg -l hlr.sqlite3 -P -C --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Tape ctrl+shift+T
## Terminal 5
```
lxc exec KarliBTS -- osmo-bts-trx -c osmo-bts.cfg --debug DRSL:DOML:DLAPDM 
```



# CODE MANAGING BTS
```
lxc exec KarliBTS -- telnet localhost 4242
```
```
lxc exec KarliBTS -- telnet localhost 4241
```
# CODE USSD PHONE GETTING NUMBER MSISDN OR EXTENSION ON THE NETWORK
```
*#100#
```

# EXPORTING IMAGE CONTAINER
```
lxc publish KarliBTS --alias KarliBTS -f
```
```
lxc image export KarliBTS .
```
There a two images exported (for mine)
* with /var/cache/apt/archives [IMAGES](https://drive.google.com/file/d/102hvWSVboSYmcFrr9yrGPMGJiNuouiEp/view?usp=sharing):  c928e6171521f687cc227ea1d4e3fec6f43c68aeecf11fb909df9588034b6ce3.tar.gz
```
md5sum  c928e6171521f687cc227ea1d4e3fec6f43c68aeecf11fb909df9588034b6ce3.tar.gz
```
```
7a6f181cbde8701956e9d6a699c8b3c0
```
* without /var/cache/apt/archives [IMAGES](https://drive.google.com/file/d/1fsdX7I6Z8ujKjUJu77AbjBPlKuDEoI-P/view?usp=drive_link): a89e0a08c6f7e80a2596b47b712ecb7fc933fb0d393e3895d90c5fc720d66087.tar.gz 
```
md5sum a89e0a08c6f7e80a2596b47b712ecb7fc933fb0d393e3895d90c5fc720d66087.tar.gz
```
```
aab88e06b2113ec99e1a04231a5b9f37 
```

# IMPORTING IMAGE
```
lxc image import a89e0a08c6f7e80a2596b47b712ecb7fc933fb0d393e3895d90c5fc720d66087.tar.gz --alias KarliBTSimage
```
```
lxc launch KarliBTSimage KarliBTS
```

# RUNNING SPOOFING EXTENSION WITH SCRIPT1
Tape ctrl+shit+T
```
lxc exec KarliBTS -- bash
```
```
wget https://raw.githubusercontent.com/SitrakaResearchAndPOC/nitb-script-all/main/osmo-nitb-scripts.zip
```
```
apt-get install zip
```
```
unzip osmo-nitb-scripts.zip
```
```
rm -rf osmo-nitb-scripts.zip
```
```
mv osmo-nitb-scripts/scripts_spoof1 ../root
```
```
mv osmo-nitb-scripts/scripts_spoof2 ../root
```
```
cd  scripts_spoof1
```
```
nano finding_imsi_extenstion.sh
```
Change sqlite file as following /root/hlr.sqlite3
```
bash finding_imsi_extenstion.sh
```
```
nano delete_all.sh
```
Change sqlite file as following /root/hlr.sqlite3
```
nano set_imsi_extension.sh
```
Change sqlite file as following /root/hlr.sqlite3
```
nano sending_sms_broadcast.py
```
Change sqlite file as following /root/hlr.sqlite3
```
exit
```



# REMARK AS SPOOF2
```
lxc exec KarliBTS -- bash
```
```
cd  scripts_spoof2
```
```
nano show_subscribers.py
```

Change sqlite file as following /root/hlr.sqlite3
```
nano sms_broadcast.py
```
Change sqlite file as following /root/hlr.sqlite3
```
nano sms_send_source_dest_msg.py
```
Change sqlite file as following /root/hlr.sqlite3


Spoof script2 modification
```
lxc exec KarliBTS -- nano scripts_spoof2/sms_send_source_dest_msg.py 
```

Before launching sms_send_source_dest_msg.py , please corret the help, change : 
```
usage: ./sms_broadcast.py extension message
This script sends a message from the specified extension (number) to all devices connected to this base station
```
to
```
usage: ./sms_send_source_dest_msg.py  extension_source extension_destination  message
This script sends a message from the specified extension source (number) to extension destination connected to this base station
```
```
exit
```

# TESTING SPOOFING1
```
lxc exec KarliBTS -- scripts_spoof1/finding_imsi_extenstion.sh
```
```
lxc exec KarliBTS -- scripts_spoof1/delete_all.sh
```
Change with the following imsi 
```
lxc exec KarliBTS -- scripts_spoof1/set_imsi_extension.sh 646040227957426 0341220590
```
```
lxc exec KarliBTS -- python2 scripts_spoof1/sending_sms_spoof_byextension.py  
```
```
lxc exec KarliBTS -- python2 scripts_spoof1/sending_sms_broadcast.py 
```

# TESTING SPOOFING2
```
lxc exec KarliBTS -- python2 scripts_spoof2/show_subscribers.py
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_broadcast.py 0341220590 'alert corona'
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_send_source_dest_msg.py 0341220590 0341220590 "alert corona v2"
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_spam.py 0341220590 3 "alert corona 3"
```

# DELETING DEVICES AT LXC
```
lxc config device remove KarliBTS ttyUSB0
```
```
lxc config device remove KarliBTS ttyUSB1 
```

## SAVING FINAL IMAGE :

* WITH CACHE /var/cache/apt/archives AT [IMAGE](https://drive.google.com/file/d/1kIobHz4emtq94L8TLAzUOh5kTjJC1hPC/view?usp=drive_link)
```
lxc publish KarliBTS --alias KarliBTS -f
```
Instance published with fingerprint: ceb29fbf547e5d34e56a5e4b499f492853fbc969e1d1bdcf8e813b0a071a2b08
```
lxc image export KarliBTS .
```
```
md5sum ceb29fbf547e5d34e56a5e4b499f492853fbc969e1d1bdcf8e813b0a071a2b08.tar.gz
```
3d1492f27800890a0b11d1aebc29c980  ceb29fbf547e5d34e56a5e4b499f492853fbc969e1d1bdcf8e813b0a071a2b08.tar.gz
```
chmod 777 ceb29fbf547e5d34e56a5e4b499f492853fbc969e1d1bdcf8e813b0a071a2b08.tar.gz
```

* WITHOUT CACHE IN /var/cache/apt/archives AT (IMAGE)[https://drive.google.com/file/d/1Y7KEpbL-ZFKeL5-9OFBy9lh2ifav9Jcy/view?usp=drive_link]
```
lxc publish KarliBTS --alias KarliBTS -f
```
Instance published with fingerprint: 7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50
```
lxc image export KarliBTS .
```
```
md5sum 7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50.tar.gz
```
0db0473d307a9cd6af3554f7eeedbec9  7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50.tar.gz
```
chmod 777 7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50.tar.gz
```

# QUICK INSTALL FOR KarliBTS_LXD
## Installation with LXC  using ubuntu if not yet installed
```
apt update
```
```
apt-get install lxd lxd-client
```


## Installation with LXC  using DragonOS if not yet installed
```
apt update
```
```
apt-get install snapd  
```
```
snap install lxd  
```

```
snap install lxd-client  
```

## Initialisation of LXD if not yet installed
```
lxd init  
```
For questions please follow the default option, an image illustration is at [image](https://github.com/SitrakaResearchAndPOC/QCSuper/blob/main/screen.jpg) 
  
User need to be in group lxd :
```
sudo usermod -a G lxd $USER  
```
or  
```
sudo /usr/sbin/usermod lxd $USER  
```

$PATH need to contains /usr/local/bin, verify with :  
```
echo $PATH  
```

if not, setup this, or add this in your .bashrc or .zshrc or ...  

```
export PATH=$PATH:/usr/local/bin  
```
# IMPORTING IMAGES FOR QUICK INSTALL
## downloading and importing image for Quick install
```
wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=1Y7KEpbL-ZFKeL5-9OFBy9lh2ifav9Jcy' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1Y7KEpbL-ZFKeL5-9OFBy9lh2ifav9Jcy" -O 7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50.tar.gz   && rm -rf /tmp/cookies.txt  
```
```
lxc image import 7dddb5be252c4c64e65898daa6cf239893a17c6d751a2f3c4db65a095ce25b50.tar.gz --alias KarliBTSimage
```
```
lxc launch KarliBTSimage KarliBTS
```

# LAUNCHING FOR QUICK INSTALL
## Adding devices on lxc
Plug usb ttl for motorola phone
## Finding all devices
```
dmesg | grep ttyUSB*
```
## Adding devices on LXC
* For on Phone (SMS only)
```
lxc config device add KarliBTS ttyUSB0 unix-char path=/dev/ttyUSB0
```  
* For two Phones (SMS and Call)
```
lxc config device add KarliBTS ttyUSB0 unix-char path=/dev/ttyUSB0
```
```
lxc config device add KarliBTS ttyUSB1 unix-char path=/dev/ttyUSB1
```
## Setting privileges
```
lxc config set KarliBTS security.privileged=true
```

## SMS ONLY For Quick install
### Terminal 1 
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB0 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin
```
### or Terminal 1
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB0 -c trx.highram.bin
```
Tape ctrl+shift+T

### Find ARFCN
Tape `*#*#4636#*#*` and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as ARFCN  

### Terminal 2
```
lxc exec KarliBTS -- osmocom/trx/src/host/layer23/src/transceiver/transceiver -a ARFCN 
```
Tape ctrl+shift+T
### Terminal 3
```
lxc exec KarliBTS -- osmo-nitb -c open-bsc.cfg -l hlr.sqlite3 -P -C --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Tape ctrl+shift+T
### Terminal 4
```
lxc exec KarliBTS -- osmo-bts-trx -c osmo-bts.cfg --debug DRSL:DOML:DLAPDM 
```


## two phones : sms and call for Quick install
### Terminal 1 
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB0 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin
```
### or Terminal 1
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB0 -c trx.highram.bin
```
Tape ctrl+shift+T
### Terminal 2
```
lxc exec KarliBTS -- osmocom/trx/src/host/osmocon/osmocon -m c123xor -p /dev/ttyUSB1 -s /tmp/osmocom_l2.2 -c osmocom/trx/src/target/firmware/board/compal_e88/trx.highram.bin 
```
### or Terminal 2
```
lxc exec KarliBTS -- ./osmocon -m c123xor -p /dev/ttyUSB1 -s /tmp/osmocom_l2.2 -c trx.highram.bin 
```
Tape ctrl+shift+T

### Find ARFCN
Tape `*#*#4636#*#*` and choose GSM only on your Android phone  
Installing network signal guru on your android phone  
And finding the arfcn that this one is connect  
Let's name this arfcn as ARFCN  

### Terminal 3
```
lxc exec KarliBTS -- osmocom/trx/src/host/layer23/src/transceiver/transceiver -a ARFCN -2
```
Tape ctrl+shift+T
### Terminal 4
```
lxc exec KarliBTS -- osmo-nitb -c open-bsc.cfg -l hlr.sqlite3 -P -C --debug=DRLL:DCC:DMM:DRR:DRSL:DNM
```
Tape ctrl+shift+T
### Terminal 5
```
lxc exec KarliBTS -- osmo-bts-trx -c osmo-bts.cfg --debug DRSL:DOML:DLAPDM 
```



## Code managing BTS
```
lxc exec KarliBTS -- telnet localhost 4242
```
```
lxc exec KarliBTS -- telnet localhost 4241
```
## Code USSD phone for getting number msisdn or extension on the network 
```
*#100#
```


## testing spoofing 1 for Quick install
```
lxc exec KarliBTS -- scripts_spoof1/finding_imsi_extenstion.sh
```
```
lxc exec KarliBTS -- scripts_spoof1/delete_all.sh
```
Change with the following imsi 
```
lxc exec KarliBTS -- scripts_spoof1/set_imsi_extension.sh 646040227957426 0341220590
```
```
lxc exec KarliBTS -- python2 scripts_spoof1/sending_sms_spoof_byextension.py  
```
```
lxc exec KarliBTS -- python2 scripts_spoof1/sending_sms_broadcast.py 
```

## testing spoofing 2 for Quick install
```
lxc exec KarliBTS -- python2 scripts_spoof2/show_subscribers.py
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_broadcast.py 0341220590 'alert corona'
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_send_source_dest_msg.py 0341220590 0341220590 "alert corona v2"
```
```
lxc exec KarliBTS -- python2 scripts_spoof2/sms_spam.py 0341220590 3 "alert corona 3"
```

## deleting device at lxc for Quick install
```
lxc config device remove KarliBTS ttyUSB0
```
```
lxc config device remove KarliBTS ttyUSB1 
```


# Remark : 
Can't run prio on lxc : Error setting SCHED_RR with prio 99

# Documentations  
* https://www.cyberciti.biz/faq/how-to-install-lxd-on-debian-11-linux/
* https://discuss.linuxcontainers.org/t/tty-device-passthrough/6108
* https://github.com/SitrakaResearchAndPOC/KarliBTS_Debian

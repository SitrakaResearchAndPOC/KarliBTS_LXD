# KarliBTS_LXD
# CREATE CONTAINERS FOR KarliBTS
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
lxc launch images:debian/10 KarliBTSLXD
```
```
lxc exec KarliBTSLXD -- sh
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
git clone git://git.osmocom.org/libosmocore.git
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
git clone git://git.osmocom.org/osmocom-bb.git
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
git clone git://git.osmocom.org/libosmo-dsp.git
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
git clone git://git.osmocom.org/osmocom-bb.git -b fixeria/trx trx
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
git clone git://git.osmocom.org/libosmo-abis.git
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
git clone git://git.osmocom.org/libosmo-netif.git
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
git clone git://git.osmocom.org/openbsc.git
```
```
cd openbsc/openbsc/
```


















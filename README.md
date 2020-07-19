# openwrt-r8169-stock
##
This is the kernel module for realtek 8169sc(l) slightly modified to run simultaneously with r8168 driver  

The source code is downloaded from https://www.realtek.com/en/component/zoo/category/rtl8169sc-l-software  

The main purpose of this package is to run r8169 and r8168 kernel modules simultaneously  

*You only need this if you want to use the r8168 driver to drive a r8168/8111 card, while still keep another r8169 card in use, since the r8168 module does not support r8169 card.*    

*You may wondoer why you can not just use both r8168 and r8169, and that is how Lean does in [coolsnowwolf/lede](https://github.com/coolsnowwolf/lede).   
The answer is, since the original r8169 driver is assosiated with the hardware ID of r8168 and other cards driven by r8168 (the linux kernel team basically wrote this driver to drive all realtek nics), run r8168 and r8169 side by side will lead to confliction problem.   
The r8169 card in my test machine is hot as boiling water when I use r8169 to drive it when using r8168 to drive another two r8168 cards. Also the r8169 card can only max out 300Mbps comparing to the other r8168 cards both maxing out 900Mbps at the same time. This is definitely a dragdown since when only this card is used and r8168 is not loaded it can also max out 900Mbps.  
To make things worse, even simply plug and unplug the cable plugged on the r8169 card can lead to crashing of the r8169 driver and softlock.*

 
## How to install r8169 stock driver on your openwrt device
1. Download r8169stock and r8168(optional) from [release](https://github.com/7Ji/openwrt-r8169-stock/releases), upload them to your device
2. Remove kmod-r8169 and r8169-firmware
````
    opkg remove kmod-r8169
    opkg remove r8169-firmware
````
3. (Optional) Install r8168, if you have not installed it
````
    opkg install kmod-r8168-xxxxx.ipk
````
4. Install r8169stock
````
    opkg install kmod-r8169stock-xxxx.ipk
````
5. Reboot if neccessary

## How to build r8169 stock driver by yourself
If you are using other versions of openwrt, or you are using a modified kernel, you may want to build this driver by yourself
1. Get the corresponding openwrt SDK or the source code, I'll just show you how to get the SDK here, since you may already know how to build it and have already built openwrt before if you have the source code.  
   (1). Go to [Download page of openwrt](https://downloads.openwrt.org/)  
   (2). Click the corresponding version, click the architecture (in our condition, x86), click the sub architecture (64 for x86-64, for example)    
   (3). Scroll down, below the images you can find the SDK with name "openwrt-sdk-[version]-[architecture]-gcc-xxxxx.tar.xz", download it  
   (4). Unarchive it
   ```
   tar -xvJf openwrt-sdk-[version]-[architecture]-gcc-xxxxx.tar.xz
   ```
2. Get into your openwrt SDK/source folder
3. Create a folder to hold both r8168 and r8169 kernel module packages (optional, this is just to make your working directory tidy)
````sh
mkdir package/realtek
````
3. Clone the r8168 driver for openwrt from BROBIRD's repository (optional)
````sh
git clone https://github.com/BROBIRD/openwrt-r8168 package/realtek/r8168
````
4. Clone the r8169 stock driver for openwrt from this repository
````sh
git clone https://github.com/7Ji/openwrt-r8169-stock package/realtek/r8169stock
````
5. Open menuconfig, the cmake should identify our two new packages in Kernel Modules/Network Devices
````sh
make menuconfig
````
6. Check r8168 and r8169stock in Kernel Modules/Network Devices, and uncheck r8169, *remember to uncheck r8169-firmware if you have saved your configuration before, in that case, r8169-firmware will not be automatically uncheck*
````sh
Kernel Modules ---> Network Devices ---> <*> r8168
Kernel Modules ---> Network Devices ---> < > r8169
Kernel Modules ---> Network Devices ---> <*> r8169stock
Firmware ---> < > r8169-firmware
````
7. Build the openwrt image (if you are using the source and that is what you want to do) or just these packages
````
make -j1 V=s
 # or
make -j1 package/realtek/r8168/compile V=s
make -j1 package/realtek/r8169stock/compile V=s
````
### Known issue:  
If you are using the openwrt source, depending on the branch you are using, i.e. v19.07.3, you may get this error while building openwrt:
````sh
gconvert.c:61:2: error: #error GNU libiconv not in use but included iconv.h is from libiconv
````
This is not the problem of this driver, it's a failure while building the toolchains.
You will need to configure glib with iconv disabled:
````sh
cd build_dir/host/pkg-config-*/glib  
./configure --enable-iconv=no --with-libiconv=gnu
````

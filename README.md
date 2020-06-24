# openwrt-r8169-stock
##
This is the kernel module for realtek 8169sc(l) slightly modified to build under openwrt environment and run simultaneously with r8168 driver  
The source code is downloaded from https://www.realtek.com/en/component/zoo/category/rtl8169sc-l-software  
The main purpose of this package is to run official r8169 and r8168 kernel modules simultaneously  
**You only need this if you want to run the official r8168 module to drive r8111/r8168 nics, while still keep another r8169 card in use**, since the official r8168 module does not support r8169 card even if you force it. And the r8169 driver provided by linux kernel will **crash frequently** if you run it with r8168.  
## How to compile r8169 stock driver in your openwrt image
1. cd into your openwrt source folder
2. create a folder to hold both r8168 and r8169 kernel module packages (optional)  
````sh
mkdir package/realtek
````
3. clone the r8168 driver for openwrt from BROBIRD's repository
````sh
git clone https://github.com/BROBIRD/openwrt-r8168 package/realtek/r8168
````
4. clone the r8169 driver for openwrt from this repository
````sh
git clone https://github.com/7Ji/openwrt-r8169-stock package/realtek/r8169stock
````
5. open menuconfig, the cmake should identify our two new packages in Kernel Modules/Network Devices
````sh
make menuconfig
````
6. check r8168 and r8169stock in Kernel Modules/Network Devices, and uncheck r8169
````sh
Kernel Modules ---> Network Devices ---> <*> r8168
Kernel Modules ---> Network Devices ---> < > r8169
Kernel Modules ---> Network Devices ---> <*> r8169stock
````
7. **IMPORTANT:** uncheck r8169-firmware in Firmware, otherwise the r8169 driver provides by linux kernel will still work, thus generating many problems
````sh
Firmware ---> < > r8169-firmware
````
8. build the openwrt image as you like  
###Known issue:  
Depending on the branch you are using, i.e. v19.07.3, you may get this error while building openwrt:
````sh
gconvert.c:61:2: error: #error GNU libiconv not in use but included iconv.h is from libiconv
````
You will need to configure glib with iconv disabled:
````sh
cd build_dir/host/pkg-config-*/glib  
./configure --enable-iconv=no --with-libiconv=gnu
````sh

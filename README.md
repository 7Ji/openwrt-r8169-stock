# openwrt-r8169-stock
This is the official kernel module for realtek 8169sc(l) slightly modified to build under openwrt environment (https://www.realtek.com/en/component/zoo/category/rtl8169sc-l-software)
The main purpose is to run official r8169 and r8168 kernel modules simultaneously
You only need this if you want to run the official r8168 module to drive r8111/r8168 nics, while still keep another r8169 card in use, since the official r8168 module does not support r8169 card, even if you force it to drive a r8169 card. And the r8169 driver in kernel will crash frequently if you run it with the r8168 module
## How to 

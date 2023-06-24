
# Root Android X96 Max Plus S905X3 and Dragonrise generic USB Joystick driver / modules
Files needed for rooting Android S905X3 like X96 Max Plus with Android 9.0 (Pie), it also included Dragonrise Inc Generic USB Joystick drivel/module for android 8/9/10 or Linux kernel 4.x.x

## Root
Tested **X96 Max Plus (Q2)** for other S905/S905X/S905X2/S905X3/S905X4 device check on https://bit.ly/rootS905
- we need **TWRP_AML_ViT_3.3.1_2020.zip** and  **Root_&_SuperSU_VS.zip**
- open **Update** app then select the twrp file, it will reboot but failed to flash, select again twrp.zip from recovery. After TWRP opened, install Root_&_SuperSU_VS.zip and it done. 

video russian tutorial https://youtu.be/73hI_71ievY we only need the visual 😁

##  Install Dragonrise Generic USB Joystick driver/Module
The Gamepad maybe has other name but it has ID **Gamepad USB\VID_0079&PID_0006**

 - Vendor ID: 0x0079 
 - Product ID: 0x0006

The **hid-dr.ko** module above is for Android 8/9/10 and above that using Linux Kernel 4.9.x ARMv7 and **Vendor_0079_Product_0006.kl** is for button configuration

-  copy *Vendor_0079_Product_0006.kl* to **/system/usr/keylayout/** change permission or chmod to  0644
- copy *hid-dr.ko* to **/system/vendor/lib/modules/** chmod to 0755
- The module need to be inserted at startup with root right, to do it automatically install **init.d scripts support** app or other method
- here the startup script to insert the module (name it like *gamepad.sh*)


      #!/system/bin/sh
      insmod /system/vendor/lib/modules/hid-dr.ko

## Remote Bluetooth
File dibawah adalah fix untuk tombol OK, Android 11 tinggal copy file `*.kl` ke `/vendor/usr/keylayout/`
- Remote Bluetooth Voice Akari AX810 `Vendor_00c4_Product_7a44.kl`
- Remote Bluetooth Voice X1 Prime (First Media) `Vendor_0416_Product_0301.kl`

Android 10 yg system-nya read-only atu 0 bytes penyimpanan instal module `module-remote-x1-prime.zip` melalui magisk

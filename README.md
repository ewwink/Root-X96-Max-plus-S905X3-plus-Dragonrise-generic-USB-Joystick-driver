

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

# Tutorial Menambahkan Remote Hybrid (Infrared IR dan Bluetooth
Saat ini remote original AX810/AT01 atau remote voice lainnya harganya  mahal, kalau beli remote merek lain yang support bluetooth pairingnya harus pakai mouse kemudian tidak bisa menyalakan STB karena ini hanya berfungsi melalui remote Infrared.

Kabar baik buat yang STB nya sudah di root bisa ikuti tutorial berikut untuk menambahkan remote hybrid (IR & Bluetooth). Di tutorial ini saya akan menggunakan STB AT01 dengan Android TV 11 dan Remote MXQ-C98 Voice yang kode nama remotenya jika di pairing adalah LM-B02, **harganya Rp 17.000** (VS remote RemoteB043 Original AX810 Rp 170.000), dan berikut langkah-langkahnya:

![Remote B043 VS LM-B02](https://github.com/ewwink/Root-X96-Max-plus-S905X3-plus-Dragonrise-generic-USB-Joystick-driver/blob/main/vs-remote.jpg)

### 1. Jika remotenya bukan LM-B02 (MXQ-C98) deteksi dulu `custom_code` nya, jika ya lewati langkah ini
- putuskan pairing remote bluetooth ke android tv boxnya
- buka `shell` atau `adb shell` di tv box dan ketik `su` 
- ketik lagi `while true; do dmesg -c | grep "meson-ir"; sleep 0.5; done`
- tekan tombol `volume +` di remote dan nanti ada error seperti `meson-ir fe084040.ir: invalid custom:0xa758f709` dimana `a7 = Checksum (abaikan)`, `58 = Scancode (volume up)` dan `f709 = custom_code`, catat **custom_code**, **scancode** dan nama tombol yang di pencet, hasilnya menjadi `0x58 115 # volume up`  angka **115** bisa samakan dan dilihat dari `/vendor/etc/remote.conf` , ulangi untuk tombol lainnya, dan hasilnya untuk LM-B02 adalah seperti berikut*:
```
# =======================================================
# Konfigurasi Multi-Remote Driver Meson-IR
# Perangkat: Remote LM-B02 (STB Advan AT01)
#  https://github.com/ewwink/Root-X96-Max-plus-S905X3-plus-Dragonrise-generic-USB-Joystick-driver
# =======================================================

custom_name = LM-B02
custom_code = 0xf709
release_delay = 80


key_begin
    0x0a 116 # LM-B02: power
    0x00 103 # LM-B02: up
    0x01 108 # LM-B02: down
    0x03 105 # LM-B02: left
    0x02 106 # LM-B02: right
    0x1f 353 # LM-B02: ok (disamakan dengan kode 353 asli B043 agar fungsi klop)
    0x0c 172 # LM-B02: home
    0x1c 158 # LM-B02: back
    0x80 189 # LM-B02: Settings (disamakan ke 189 agar sesuai ROM AT01)
    0x58 115 # LM-B02: volum up
    0x5d 114 # LM-B02: volum down
    0x0d 113 # LM-B02: mute
    0x22 60  # LM-B02: voice command (disamakan ke 60 IR Assistant)
    0x1a 580 # LM-B02: App list (disamakan ke 580 All Apps)
    0x72 59  # LM-B02: Youtube (disamakan ke 59)
    0x86 65  # LM-B02: Netflix (disamakan ke 65)
    0x4a 358 # LM-B02: mouse toggle (sementara diarahkan ke INFO 358)
key_end
```
tambahkan kode diatas ke file `remote.conf` dibagian paling bawah
** bisa gunakan AI untuk generate key mappping-nya

### 2. Aktifasi Remote LM-B02
untuk mengaktifkan remote LM-B02 backup `/vendor/etc/remote.conf` kemudian timpa isi teks nya dengan yang ada di file [remote-B043-dan-LM-B02.conf](https://raw.githubusercontent.com/ewwink/Root-X96-Max-plus-S905X3-plus-Dragonrise-generic-USB-Joystick-driver/refs/heads/main/remote-B043-dan-LM-B02.conf) dan reboot stb-nya.

### 3. Fix tombol Power tidak berfungsi di mode bluetooth
jika tombol power tidak berfungsi saat bluetooth (saat remote tidak di arahkan ke STB) buka shell command  lagi dan jalankan perintah `dumpsys input`  contoh outputnya:
```
 11: LM-B02 Consumer Control
      Classes: 0x80000121
      Path: /dev/input/event10
      Enabled: true
      Descriptor: 1c47d139382e6058a404cbccac783373dcb1a0ee
      Location:
      ControllerNumber: 0
      UniqueId: 25:02:35:00:1e:0c
      Identifier: bus=0x0005, vendor=0x1d5a, product=0xc081, version=0x0101
      KeyLayoutFile: /vendor/usr/keylayout/Vendor_1d5a_Product_c081.kl
```
catat teks seperti `0x1d5a, product=0xc081`  kemudian di folder `/vendor/usr/keylayout/` cari file `Vendor_1d5a_Product_c081.kl` jika belum ada buat dan isi teks-nya dari file lain, tapi  jika sudah ada edit `/vendor/usr/keylayout/Vendor_1d5a_Product_c081.kl` ubah baris
```
key 116 POWER
```
menjadi
```
key 116 POWER WAKE
```
restart lagi stb-nya dan selesai




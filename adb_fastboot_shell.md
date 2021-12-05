# Lệnh ADB - Fastboot - Shell thông dụng

- [Lệnh ADB - Fastboot - Shell thông dụng](#lệnh-adb---fastboot---shell-thông-dụng)
  - [ADB](#adb)
    - [Cài file apk](#cài-file-apk)
    - [Copy files vào devices](#copy-files-vào-devices)
    - [Yêu cầu quyền root](#yêu-cầu-quyền-root)
    - [Reset devices](#reset-devices)
    - [Restart](#restart)
  - [Shell (tập lệnh) của device](#shell-tập-lệnh-của-device)
    - [Khởi động vào bootloader](#khởi-động-vào-bootloader)
    - [Chạy file sh trong sdcard](#chạy-file-sh-trong-sdcard)
    - [Truy cập sdcard](#truy-cập-sdcard)
    - [Khởi động ứng dụng](#khởi-động-ứng-dụng)
    - [Xoá thư mục](#xoá-thư-mục)
    - [Xoá file](#xoá-file)
    - [Hiện tất cả ip](#hiện-tất-cả-ip)
  - [Fastboot](#fastboot)
    - [Danh sách device connect](#danh-sách-device-connect)
    - [Khởi động file img](#khởi-động-file-img)
    - [Hiện điện thoại kết nối](#hiện-điện-thoại-kết-nối)
    - [Khởi động lại bình thường](#khởi-động-lại-bình-thường)
    - [Mở khoá Bootloader](#mở-khoá-bootloader)
    - [Khoá lại bootloader](#khoá-lại-bootloader)
    - [Khởi động vào Recovery](#khởi-động-vào-recovery)
    - [Khởi động vào Bootloader](#khởi-động-vào-bootloader-1)
    - [Cài boot.img](#cài-bootimg)
    - [Chỉ chạy file image_file.img](#chỉ-chạy-file-image_fileimg)
    - [Format bộ nhớ điện thoại](#format-bộ-nhớ-điện-thoại)
    - [Xoá bộ nhớ](#xoá-bộ-nhớ)
  - [Cú pháp file Bat (window)](#cú-pháp-file-bat-window)
  - [Reference](#reference)

## ADB

```quote
### Danh sách device kết nối
adb devices

// Output
List of devices attached
be601824        device
```

### Cài file apk

```quote
adb install -r amazmod\service-release.apk
```

### Copy files vào devices

```quote
adb push rom\start_update.sh /sdcard/
adb push rom\recovery.img /sdcard/
```

### Yêu cầu quyền root

```quote
adb root
adb remount rw
```

### Reset devices

```quote
adb reboot wipe
```

### Restart

```quote
adb reboot
```

## Shell (tập lệnh) của device

```quote
adb root
adb shell

// Output
root@watch:/ #

// OR
adb shell <lệnh>
```

### Khởi động vào bootloader

```quote
adb shell reboot bootloader
```

### Chạy file sh trong sdcard

```quote
adb shell cd /sdcard/; sh start_update.sh
```

### Truy cập sdcard

```quote
adb shell cd /sdcard/
```

### Khởi động ứng dụng

```quote
adb shell monkey -p com.amazmod.service 1
```

### Xoá thư mục

```quote
adb shell rm -r /sdcard/supersu/
```

### Xoá file

```quote
adb shell rm /sdcard/recovery.img
```

### Hiện tất cả ip

```quote
// Android 7
adb shell ifconfig
adb shell ip address show

// Android 5
adb shell netcfg | grep wlan0
```

## Fastboot

### Danh sách device connect

```quote
fastboot devices
```

### Khởi động file img

```quote
fastboot boot root\boot-US-adb-root.img
```

### Hiện điện thoại kết nối

```quote
fastboot devices
```

### Khởi động lại bình thường

```quote
fastboot reboot
```

### Mở khoá Bootloader

```quote
fastboot oem unlock
```

### Khoá lại bootloader

```quote
fastboot oem lock
```

### Khởi động vào Recovery

```quote
fastboot reboot recovery
```

### Khởi động vào Bootloader

```quote
fastboot reboot bootloader
// When you reboot the bootloader or fastboot without flashing the images, this command will help.
```

### Cài boot.img

```quote
fastboot flash boot.img
// This command can be used for flashing boot.img
```

### Chỉ chạy file image_file.img

```quote
fastboot boot image_file.img
// Want to just boot your device temporarily into custom recovery mode without flashing? Use this command.
```

### Format bộ nhớ điện thoại

```quote
fastboot format:ex4 userdata
// To erase your device’s data, this command will be used.
```

### Xoá bộ nhớ

```quote
fastboot erase
// Fastboot erase command will erase items from your device. You can wipe data from different partitions on your device.
```

## Cú pháp file Bat (window)

```quote
echo  0. Restarting adb server
echo **************************
adb.exe kill-server >nul 2>&1
adb.exe start-server >nul 2>&1
echo ********************************
echo  1. Connect the watch to the PC 
echo ********************************
adb.exe wait-for-device
adb.exe devices >nul 2>&1
adb.exe root
adb.exe remount
echo ******************
echo  2. Copying files 
echo ******************
adb.exe push mcu\old\. /sdcard/firmware
adb.exe push mcu\old\. /system/etc/firmware
adb.exe push mcu\old\. /system/mcu
echo **************
echo  3. Rebooting
echo **************
adb.exe reboot
adb.exe wait-for-device
echo *******************************************
echo  4. Updating Ultra Mode resources %rom_v2%
echo *******************************************
adb.exe shell mcu_res_upgrade -r /sdcard/firmware -z /sdcard/firmware
echo *********************************
echo  5. Checking Ultra Mode resources 
echo *********************************
adb.exe shell check_mcu_res_upgrade.sh
adb.exe shell installsensorhub.sh
echo ********************
echo  6. Removing files
echo ********************
adb.exe shell rm -r /sdcard/firmware
echo ***************
echo  7. Rebooting
echo ***************
adb.exe reboot
adb.exe wait-for-device
adb.exe kill-server >nul 2>&1
echo ************
echo  8. Finish
echo ************
pause
```

## Reference

- <https://nerdschalk.com/how-to-install-adb-and-fastboot/>
# Lệnh ADB - Fastboot thông dụng

- [Lệnh ADB - Fastboot thông dụng](#lệnh-adb---fastboot-thông-dụng)
  - [ADB](#adb)
    - [Khởi động vào bootloader](#khởi-động-vào-bootloader)
    - [Copy files vào devices](#copy-files-vào-devices)
    - [Xoá thư mục](#xoá-thư-mục)
    - [Xoá file](#xoá-file)
    - [Yêu cầu quyền root](#yêu-cầu-quyền-root)
    - [Reset devices](#reset-devices)
    - [Restart](#restart)
    - [Chạy file sh trong sdcard](#chạy-file-sh-trong-sdcard)
    - [Truy cập vào Shell (tập lệnh) của device](#truy-cập-vào-shell-tập-lệnh-của-device)
      - [Truy cập sdcard](#truy-cập-sdcard)
    - [Cài file apk](#cài-file-apk)
    - [Khởi động ứng dụng](#khởi-động-ứng-dụng)
  - [Fastboot](#fastboot)
    - [Danh sách device connect](#danh-sách-device-connect)
    - [Khởi động file img](#khởi-động-file-img)
  - [Cú pháp file Bat (window)](#cú-pháp-file-bat-window)
  - [Reference](#reference)

## ADB

```
### Danh sách device kết nối
adb devices

// Output
List of devices attached
be601824        device
```

### Khởi động vào bootloader

```
adb shell reboot bootloader
```

### Copy files vào devices

```
adb push rom\start_update.sh /sdcard/
adb push rom\recovery.img /sdcard/
```

### Xoá thư mục

```
adb shell rm -r /sdcard/supersu/
```

### Xoá file

```
adb shell rm /sdcard/recovery.img
```

### Yêu cầu quyền root

```
adb root
adb remount rw
```

### Reset devices

```
adb reboot wipe
```

### Restart

```
adb reboot
```

### Chạy file sh trong sdcard

```
adb shell cd /sdcard/; sh start_update.sh
```

### Truy cập vào Shell (tập lệnh) của device

```
adb root
adb shell

// Output
root@watch:/ #
```

#### Truy cập sdcard

```
adb shell cd /sdcard/
```

### Cài file apk

```
adb install -r amazmod\service-release.apk
```

### Khởi động ứng dụng

```
adb shell monkey -p com.amazmod.service 1
```


## Fastboot

### Danh sách device connect

```
fastboot devices
```

### Khởi động file img

```
fastboot boot root\boot-US-adb-root.img
```

```
fastboot devices
// Check whether the device is detected and recognized by the computer

fastboot reboot
// Reboot the Android devices into the recovery mode

fastboot oem unlock
// Unlock the bootloader on with fastboot OEM unlock

fastboot oem lock
// After unlocking the command, relock it using this command

fastboot reboot recovery
// Reboot the device into recovery

fastboot reboot bootloader
// When you reboot the bootloader or fastboot without flashing the images, this command will help.

fastboot flash boot
// This command can be used for flashing boot.img

fastboot boot image_file.img
// Want to just boot your device temporarily into custom recovery mode without flashing? Use this command.

fastboot format:ex4 userdata
// To erase your device’s data, this command will be used.

fastboot erase
// Fastboot erase command will erase items from your device. You can wipe data from different partitions on your device.
```

## Cú pháp file Bat (window)

```
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
Raspberry Pi 4 boot EEPROM
==
官方文档：https://www.raspberrypi.org/documentation/hardware/raspberrypi/booteeprom.md
升级
==
Updating from Raspberry Pi OS
--
Bootloader updates are instigated during a normal apt update, apt full-upgrade cycle, this means you will get new features and bug fixes during your normal updates.

Bootloader updates are performed by rpi-eeprom-update service provided by the rpi-eeprom package. This service runs at boot and updates the bootloader at the next reboot if a new production release is available. The service automatically migrates the current boot settings to the new bootloader release.

To update your system, including the bootloader:

```
sudo apt update
sudo apt full-upgrade
sudo reboot
```
也就是说在更新系统的时候同时更新eeprom

Manually checking if an update is available
--
提前说一句，手动检查更新，必须更新rpi-eeprom这个软件包，单独更新可以使用安装命令。
Running the rpi-eeprom-update command with no parameters indicates whether an update is required. An update is required if the version of the most recent file in the firmware directory (normally /lib/firmware/raspberrypi/bootloader/critical) is newer than that reported by the current bootloader. The images under /lib/firmware/raspberrypi/bootloader are part of the rpi-eeprom package and are only updated via apt upgrade.

`sudo rpi-eeprom-update`
If an update is available, you can install it using:

`sudo rpi-eeprom-update -a`
`sudo reboot`
Reading the current EEPROM version
`vcgencmd bootloader_version`

Firmware release status
--
The firmware release status corresponds to a particular subdirectory of bootloader firmware images (/lib/firmware/raspberrypi/bootloader/...), and can be changed to select a different release stream. By default, Raspberry Pi OS only selects critical updates (security fixes or major hardware compatiblity changes) since most users do not use alternate boot modes (TFTP, USB etc)

* critical - Default - rarely updated
* stable - Updated when new/advanced features have been successfully beta tested.
* beta - New or experimental features are tested here first.

Since the release status string is just a subdirectory name then it's possible to create your own release streams e.g. a pinned release or custom network boot configuration.

Changing the firmware release
You can change which release stream is to be used during an update by editing the /etc/default/rpi-eeprom-update file and changing the FIRMWARE_RELEASE_STATUS entry to the appropriate stream.

For more information about the rpi-eeprom-update configuration file please run `rpi-eeprom-update -h`.
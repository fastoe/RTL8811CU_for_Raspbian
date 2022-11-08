# Realtek 8811CU driver for Raspberry Pi OS

Driver for 802.11ac USB adapter with RTL8811CU chipset, only STA/Monitor mode is supported, no AP mode.

A few known wireless cards that use this driver include:
* [Fastoe AC650 USB Wi-Fi Adapter](https://amzn.to/2KR1Lxi)
* TOTOLINK A650UA v3
* D-Link - DWA-171C
* BrosTrend AC5L
* EDUP EP-AC1651
* EDUP EP-AC1635
* Cudy WU700

### Tested with Raspberry Pi OS (32-bit):
- [Kernel 5.15.61](https://downloads.raspberrypi.org/raspios_full_armhf/images/raspios_full_armhf-2022-09-26/)
- [Kernel 5.10.17](https://downloads.raspberrypi.org/raspios_full_armhf/images/raspios_full_armhf-2021-05-28/)
- [Kernel 5.4.83](https://downloads.raspberrypi.org/raspios_full_armhf/images/raspios_full_armhf-2021-01-12/)
- [Kernel 5.4.79](https://downloads.raspberrypi.org/raspios_full_armhf/images/raspios_full_armhf-2020-12-04/)
- [Kernel 4.19.118](https://downloads.raspberrypi.org/raspios_full_armhf/images/raspios_full_armhf-2020-05-28/)
- Kernel 4.19.50

### Tested Raspberry Pi Devices:
- Raspberry Pi 400
- Raspberry Pi 4 Model B 2G
- Raspberry Pi 4 Model B 4G
- Raspberry Pi 4 Model B 8G
- Raspberry Pi Zero W
- Raspberry Pi Zero v1.3
- Raspberry Pi 3 B+
- Raspberry Pi 2 B

### Installation Information

To build, you have to retrieve source and run `make`, do following:

- For Raspberry Pi OS kernel 5.11 or later, please clone the v5.8.7 branch:
  ```bash
  sudo apt install -y bc git dkms build-essential raspberrypi-kernel-headers
  git clone -b v5.8.7 https://github.com/fastoe/RTL8811CU_for_Raspbian
  cd RTL8811CU_for_Raspbian
  make
  sudo make install
  sudo modprobe 8821cu
  sudo reboot
  ```

- For Raspberry Pi OS 5.10 kernel, clone the v5.8.1 branch:
  ```bash
  sudo apt install -y bc git dkms build-essential raspberrypi-kernel-headers
  git clone -b v5.8.1 https://github.com/fastoe/RTL8811CU_for_Raspbian
  cd RTL8811CU_for_Raspbian
  make
  sudo make install
  sudo modprobe 8821cu
  sudo reboot
  ```

- For Raspberry Pi OS 5.4 or earlier kernel:
  ```bash
  sudo apt install -y bc git dkms build-essential raspberrypi-kernel-headers
  git clone https://github.com/fastoe/RTL8811CU_for_Raspbian
  cd RTL8811CU_for_Raspbian
  make
  sudo make install
  sudo modprobe 8821cu
  sudo reboot
  ```

### If fails to compile like `/lib/modules/5.x.x-v7+/build: No such file or directory.  Stop`:
```
pi@raspberrypi:~/RTL8812BU_for_Raspbian $ make
make ARCH=arm CROSS_COMPILE= -C /lib/modules/5.4.51-v7+/build M=/home/pi/RTL8812BU_for_Raspbian  modules
make[1]: *** /lib/modules/5.x.x-v7+/build: No such file or directory.  Stop.
make: *** [Makefile:2284: modules] Error 2
```
please run the `rpi-source` command to install the kernel headers for kernel:
```
sudo apt install -y bc git flex bison libssl-dev libncurses5-dev
sudo wget https://raw.githubusercontent.com/RPi-Distro/rpi-source/master/rpi-source -O /usr/local/bin/rpi-source && sudo chmod +x /usr/local/bin/rpi-source && /usr/local/bin/rpi-source -q --tag-update
rpi-source
```
then, re-make again.

### If fails to compile like this:
```
make ARCH=arm CROSS_COMPILE= -C /lib/modules/5.4.51-v7+/build M=/home/pi/rtl8821CU  modules
make[1]: Entering directory '/home/pi/linux-f2f7e4b23d8788e96f81a7522b2f703e51c53e70'
  CC [M]  /home/pi/rtl8821CU/core/rtw_cmd.o
gcc: error: -mfloat-abi=soft and -mfloat-abi=hard may not be used together
make[2]: *** [scripts/Makefile.build:266: /home/pi/rtl8821CU/core/rtw_cmd.o] Error 1
make[1]: *** [Makefile:1709: /home/pi/rtl8821CU] Error 2
make[1]: Leaving directory '/home/pi/linux-f2f7e4b23d8788e96f81a7522b2f703e51c53e70'
make: *** [Makefile:2214: modules] Error 2
```
please run the following command:
- for AArch32 (32-bit)
```
sudo cp /lib/modules/$(uname -r)/build/arch/arm/Makefile /lib/modules/$(uname -r)/build/arch/arm/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-msoft-float//' /lib/modules/$(uname -r)/build/arch/arm/Makefile
sudo ln -s /lib/modules/$(uname -r)/build/arch/arm /lib/modules/$(uname -r)/build/arch/armv7l
```
- for AArch64 (64-bit)
```
sudo cp /lib/modules/$(uname -r)/build/arch/arm64/Makefile /lib/modules/$(uname -r)/build/arch/arm64/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-mgeneral-regs-only//' /lib/modules/$(uname -r)/build/arch/arm64/Makefile
```
then, re-make again.

Enjoy!

# Realtek 8811CU driver for Raspbian

A few known wireless cards that use this driver include:
* [Fastoe AC650 USB Wi-Fi Adapter](https://amzn.to/2KR1Lxi)
* TOTOLINK A650UA v3
* D-Link - DWA-171C
* BrosTrend AC5L
* EDUP EP-AC1651
* EDUP EP-AC1635
* Cudy WU700

Driver for 802.11ac USB adapter with RTL8811CU chipset, only STA/Monitor mode is supported, no AP mode.

Currently tested with Linux RaspberryPi 5.4.51-v7l+/4.19.118-v7+/4.19.97-v7+ on:
- Raspberry Pi 400
- Raspberry Pi 4 B
- Raspberry Pi Zero W
- Raspberry Pi Zero v1.3 (5.10.17-v7+)
- Raspberry Pi 3 B+
- Raspberry Pi 2 B

### Manual installation

To build, you have to retrieve source and run `make`, do following:

For Raspberry Pi OS 5.10 kernel, please clone the v5.8.1 branch:

```bash
sudo apt install -y bc git dkms build-essential raspberrypi-kernel-headers
git clone -b v5.8.1 https://github.com/fastoe/RTL8811CU_for_Raspbian
cd RTL8811CU_for_Raspbian
make
sudo make install
sudo modprobe 8821cu
sudo reboot
```

For Raspberry Pi OS 5.4 kernel:

```bash
sudo apt install -y bc git dkms build-essential raspberrypi-kernel-headers
git clone https://github.com/fastoe/RTL8811CU_for_Raspbian
cd RTL8811CU_for_Raspbian
make
sudo make install
sudo modprobe 8821cu
sudo reboot
```

If fails to compile like `/lib/modules/5.x.x-v7+/build: No such file or directory.  Stop`:
```
pi@raspberrypi:~/RTL8812BU_for_Raspbian $ make
make ARCH=arm CROSS_COMPILE= -C /lib/modules/5.4.51-v7+/build M=/home/pi/RTL8812BU_for_Raspbian  modules
make[1]: *** /lib/modules/5.4.51-v7+/build: No such file or directory.  Stop.
make: *** [Makefile:2284: modules] Error 2
```
please run the `rpi-source` command to install the kernel headers for kernel:
```
sudo apt install -y bc git flex bison libssl-dev libncurses5-dev
sudo wget https://raw.githubusercontent.com/RPi-Distro/rpi-source/master/rpi-source -O /usr/local/bin/rpi-source && sudo chmod +x /usr/local/bin/rpi-source && /usr/local/bin/rpi-source -q --tag-update
rpi-source
```
then, re-make again.

If fails to compile like this:
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
```
# for AArch32
sudo cp /lib/modules/$(uname -r)/build/arch/arm/Makefile /lib/modules/$(uname -r)/build/arch/arm/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-msoft-float//' /lib/modules/$(uname -r)/build/arch/arm/Makefile
sudo ln -s /lib/modules/$(uname -r)/build/arch/arm /lib/modules/$(uname -r)/build/arch/armv7l

# for AArch64
sudo cp /lib/modules/$(uname -r)/build/arch/arm64/Makefile /lib/modules/$(uname -r)/build/arch/arm64/Makefile.$(date +%Y%m%d%H%M)
sudo sed -i 's/-mgeneral-regs-only//' /lib/modules/$(uname -r)/build/arch/arm64/Makefile
```
then, re-make again.

Enjoy!

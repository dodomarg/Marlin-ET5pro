## This implimentation

Is meant for the Anet ET5Pro at the T.A.M.I. hackerspace and should represent the current version of firmware built on the machine, if you change the firmware please update this repo accordingly.

![Visit our Wiki!](https://wiki.telavivmakers.org/index.php/Main_Page)

# Marlin for ET4/5 series 3D printers
![counter](https://enoreg85bg22wzz.m.pipedream.net)

This project is an effort to try to adapt the Anet ET4/ET5 motherboard and display for use with Marlin.
Anyone can contribute to completing this project. Feedback is also welcome.

# Context

As I don't have a blog, I would like to leave a personal opinion here related with this project experience. To whom it may concern.

I started this project as a hobby, on the one hand in response to the demand from the Anet ET series user community (including myself) for a quality and versatile firmware for their printers, and on the other hand, of Anet's refusal to provide solutions to problems and shortcomings of the stock firmware (a [change.org](https://www.change.org/p/anet-open-firmware-for-the-anet-et4) was even launched)

Without much knowledge of either Marlin or the closed ET hardware, and with help from the community, we have Marlin in the ET series from some time now. It is hard to believe that a company that claims to be a leader in the 3D printing industry will not be able to employ some engineering resources to port Marlin. Or even to improve its own firmware, which most remarkable milestone on recent released V3.0.0 (jump from V1.x), is to support some new languages... 

It was also a surprise (or not so much seeing it in perspective) that, in a very timely manner, Anet contacted me to get on the open source bandwagon and offer me a paid collaboration, which, although it never took place, they rushed to make it public along with an open source campaign, providing Anet a kind of reconciliation with the community appearing to be involved in this project, and a way of selling more printers. Objective accomplished.

In summary, after some time dealing with Anet, my personal experience has been regrettable. The lack of seriousness shown by the company (even the CEO) is hard to believe. The only concern in this whole project has been from the marketing and sales point of view, never from their users needing, as they want to do see. Anet's style is more in line with the "let them solve it themselves, and we will take advantage of it" of a mediocre company of the heap than with the innovative company, concerned about the user experience that they want to pretend to be (or show).

## Current status

### Working:
  - XYZ Steppers
  - Extruder
  - SD Card
  - USB comunication / pronterface
  - TFT
  - Filament runout detector
  - EEPROM (Flash emulation)
  - Powerloss (Thanks to Zhiniukas & SidDrP)
  - [Bltouch](/docs/Tutorials/bltouch-en.md)
  - PC/SD firmware load/update
    - I've managed to get working OpenBLT, (PC-USB / SD / DFU) updates. ~~I'm still looking for a way to do a first time flash without flasher~~. A hardware flasher is very recommended for its price.
  - Onboard EEPROM I2C (4Kb/512B)
    - Adapted and working, but not usable. Onboard EEPROM IC is too small to store marlin config, so, expect EEPROM errors if used. Use FLASH_EEPROM_EMULATION instead or solder another 24CXX EEPROM IC (24C32 or above recommended).
  - Onboard FLASH (128Mb/16MB)
    - Ready. Tested LVGL from MKS (ET5 TFT). Not used, as not dedicated or universal LVGL UI has been designed AFAIK.
### On progress:
  - No work in progress
### Known bugs:

I have enabled issues tab. Please, try to be detailed regarding use cases and other useful information like hardware and software context. 
To get help on creating an issue see [this](https://docs.github.com/en/enterprise/2.15/user/articles/creating-an-issue).

## Build/Installation

  DISCLAIMER: Not for production use. This is an unfinished project and under development. I am not responsible for what may happen to the motherboard or printer. Use only at your own risk.

Currently you can only flash this firmware using a flasher (stlink, jlink, bmp etc), **unless** you already have flashed the openblt bootloader (BL).

## Before flashing this firmware (optional, but recommended):  

First time, I recommend making a backup of your firmware. At least your bootloader (addresses from 0x08000000 to 0x08010000). This way, you can always recover/return to stock firmware by:  
  1. flashing the bootloader backup, from address 0x08000000 to 0x8010000.  
  2. flashing any of the available Anet firmwares from address 0x08010000 onwards.  

If you don't perform this step, and, just in case of brick, there are copies of stock firmware ET4 releases and bootloader below on [resources](#resources) section.

## Considerations

Despite of using "ET4" naming, this firmware is valid for both ET4 and ET5 printers. Both printers mount the same board up until now.

You have two options to install/update this firmware:
- **Option A** > ***Not using a bootloader (BL)*** 
  - Firmware (Marlin) starts directly when you switch on the printer.
  - You need the flasher to perform firmware installation/updates.
  - Environment needs to be set to **Anet_ET4_no_bootloader**.
- **Option B** > ***Using a bootloader along with this firmware***
  - When you switch on the printer, bootloader (BL) starts first, and, if there are not any firmware updates incomming (on SD or through PC-USB), it loads the firmware (Marlin).
  - You need the flasher to **flash the BL** the first time. Once it is flashed, you **MUST** install/update your FW through your **BL**, either by **SD card or USB-PC**. You **MUST NOT** use the **flasher** to install/update the FW, as it will not work.
  - Environment needs to be set to **Anet_ET4_OpenBLT**.

## Step One: Building from sources

1. Download or clone this [repo](https://github.com/davidtgbe/Marlin/archive/master.zip). Ensure you build the firmware with **latest sources**, as firmware.srec file will not be built with older sources.

2. You have two options to configure your own Marlin build:
- a) **Recommended**. Use [EasyConfig](/Marlin/EasyConfig.h) for a simple configuration experience.
    - Make sure you read the whole EasyConfig.h file carefully. It contains instructions to configure it.
    - EasyConfig will override some paremeters of configuration.h and configuration_adv.h.
- b) Configure your build from scratch.
    - [User configurations](/config) **are neither updated nor maintaned**. Please, read the readme.md inside the config folder. **Don't copy paste these files.**
    - [Marlin configurations](https://github.com/MarlinFirmware/Configurations/tree/import-2.1.x/config/examples/Anet) can also be a place to take a look. **Don't copy paste these files** as there could be a version mismatch between them and this repo.
    - Make sure to modify your config.h and config_adv.h according to your ET4/5 model (ET4, ET5, ET4 PRO, ET4+, ...)
    - Settings as driver model (A4988/TMC2208), Z endstop position (UP/DOWN), TFT resolution, XYZ size, homming offsets, auto bed levelling sensor, etc, need to be defined according to your model.
    - Fine tunning could be needed (e.g. XYZE [steps](https://marlinfw.org/docs/gcode/M092.html) or offsets, Jerks, JD, LA, etc).

4. Build project with platform.io on VS code is recommended. Default environment to build is set to **Anet_ET4_OpenBLT**.
   There are many tutorials on the web. You can follow them, **ADAPTING** steps to build this project. This one [here](/docs/Tutorials/build-es.md) in spanish made by me, and just another one [here](https://3daddict.com/marlin-2-0-beginner-guide-for-3d-printer-firmware/).
5. If everything went well, you will find marlin binary files with extension **.[elf|bin|srec]**, generated in the build output folder:
```
<src_code_base_folder>\.pio\build\ET4\
```

## Step two Flashing/Installing the firmware  

There are several tutorials available for [stlink](https://www.cnx-software.com/2020/02/04/how-to-recover-from-a-bad-firmware-upgrade-on-anet-et4-3d-printer/)/[j-link](https://danielabalo.wordpress.com/flasear-anet-et4/) flashers. Take a look to them.

You have two options to install/update this firmware:
- **Option A >** If you are going to use the firmware **without** bootloader (environment Anet_ET4_no_bootloader):
  -  It is assumed you have built your firmware with no offset.
  -  As the firmware has been built without offset, and lacks of a bootloader, you have to just flash your firmware binary file (step 1.5) with your preferred flasher from address **0x8000000**.
- **Option B >** If you are going to use the firmware **with** bootloader (environment Anet_ET4_OpenBLT):
  - You need to flash the bootloader from address **0x08000000**. This step needs to be performed just once, so, you can skip this step if you have already done so. You can download the precompiled bootloader [binary](https://github.com/davidtgbe/openblt/releases), or, you can build it yourself from source code using [STM32 Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html).
  - After flashing the BL, you can disconnect your flasher, it will not be used anymore.
  - Now it is time to install de firmware. BL will assist us in this task, so, you must use a SD-CARD **OR** USB-PC/microboot to flash perform this step.
    - SD-CARD -> Copy file **firmware.srec** from build folder (step 1.5) in the root folder of the SD-CARD.
    - USB-PC/microboot -> 
      - Download microboot software. It is uploaded to github and you can download it from [here](https://github.com/davidtgbe/openblt/archive/master.zip).
      - Extract the .zip file you have just downloaded and browse to the folder **openblt/Host/**. You will find the **microboot.exe** executable.
      - Connect your printer via USB to your PC and get the COM port number.
      - Open **microboot.exe** executable and configure COM port number and speed (115200) through **settings** button. Then, click **browse** and search for the file **firmware.srec**. You will find it in the output build folder (step 1.5).
  - Switch off and then switch on the printer to begin the installation/update process.
  - ~~Screen will be white during the process, and, after 3 or 4 minutes, Marlin will appear on the screen.~~ If you have latest OpenBLT release, you will see the update process on screen and Marlin will start after flashing process.

You can connect with pronterface to corresponding COM port @250000bps.

### Flashing considerations

If you use bootloader, you **must not use the flasher to install/update the FW**. The bootloader inserts a special **checksum** in the firmware during the install/update process. Bootloader **checks** for this checksum before jumping to the firmware. If you use your **flasher** to install the firmware, this **checksum is not written**, and, therefore, bootloader **will not boot** the firmware ~~, and **your screen will be white**.~~ and OpenBLT will ask you to flash a firmware.

**DFU** mode (Device Firmware Upgrade) has been added to the bootloader. You can enter to it just by **pressing touchscreen while switching printer on**. By using DFU mode you can even update your bootloader without needing a hardware flasher, just using the PC-USB and the right [tool](https://www.st.com/en/development-tools/flasher-stm32.html).


*More info:*  
[SD-CARD update process](https://www.feaser.com/openblt/doku.php?id=manual:sdcard_demo)  
[PC-USB update process](https://www.feaser.com/openblt/doku.php?id=manual:microboot)
[OpenBLT project](https://github.com/davidtgbe/openblt)

## Hardware

![Board](docs/Tutorials/media/et4board.jpg)

MCU: [STM32F407VGT6 ARM CORTEX M4](https://www.st.com/resource/en/datasheet/dm00037051.pdf)  
DRIVERS: [TMC2208 (silent)](https://www.trinamic.com/fileadmin/assets/Products/ICs_Documents/TMC220x_TMC2224_datasheet_Rev1.09.pdf) / [A4988 (noisy)](https://www.pololu.com/file/0J450/a4988_DMOS_microstepping_driver_with_translator.pdf)  
USB TO SERIAL CONVERTER: [CH340G](https://www.mpja.com/download/35227cpdata.pdf)  
SERIAL FLASH MEMORY: [WINBOND W25Q128JVSQ (128M-bit)](https://www.winbond.com/resource-files/w25q128jv%20revf%2003272018%20plus.pdf)  
EEPROM: [AT24C04C (ATMLH744 04CM) 4 Kb](https://datasheet.lcsc.com/szlcsc/1809192313_Microchip-Tech-AT24C04C-SSHM-T_C6205.pdf)  
LCD ET4: [ST7789 @ 320x240 | STP320240_0280E2T (40P/1,5): ST7789 (YT280S008)](https://a.aliexpress.com/_dV4Bghv) | [ST7789V](https://www.crystalfontz.com/controllers/Sitronix/ST7789V/470/)  
LCD ET5: [ST7796S @ 480x320](https://www.displayfuture.com/Display/datasheet/controller/ST7796s.pdf)  
TOUCH: [XPT2046](https://ldm-systems.ru/f/doc/catalog/HY-TFT-2,8/XPT2046.pdf)  
MOSFETS (BED/HOTEND): [G90N04](https://datasheet.lcsc.com/szlcsc/1811281041_GOFORD-G90N04_C337510.pdf)  
CLK: JF8.000 (8MHZ MCU EXT CLK)  
CLK: JF12.000 (12 MHZ USB-UART CLK)  
SCHOTTKY DIODE: [SS56](https://www.taitroncomponents.com/catalog/Datasheet/SS52.pdf)  
REGULATOR: [AMS1117 3.3 DN811](http://www.advanced-monolithic.com/pdf/ds1117.pdf)  
MOSFET: [030N06](https://www.alldatasheet.es/view.jsp?Searchword=030N06&sField=3)  
MOSFET: [A19T](https://datasheetspdf.com/pdf/1401820/UMW/A19T/1)  
STEP DOWN DC CONVERTER: [XL2596S -5.0E1 83296 ](https://datasheetspdf.com/datasheet/XL2596.html)  
VOLTAGE COMPARATOR: [293 STG9834 / LM293DT](https://www.st.com/resource/en/datasheet/lm193.pdf)  

## Pin mapping

E-STEP => PB9  
E-DIR => PB8  
E-ENABLE => PE0  
  
X-STEP => PB6  
X-DIR => PB5  
X-ENABLE => PB7  
  
Y-STEP => PB3  
Y-DIR => PD6  
Y-ENABLE => PB4  
  
Z-STEP => PA12  
Z-DIR => PA11  
Z-ENABLE => PA15  
  
Y-LIMIT => PE12  
X-LIMIT => PC13  
Z-LIMIT => PE11  
  
TEMP_BED => PA4  
TEMP_EXB1 => PA1  
  
END_FAN => PE1  
LAY_FAN => PE3  
  
END_CONTROL => PA0  
BED_CONTROL => PE2  
  
LV_DET => PC3  
MAT_DET1 => PA2  
POWER_LOSS_DET => PA8 (PANET)  
POWER_LOSS_SUPERCAP_SWITCH => PA3 (Zhiniukas & SidDrP)
  
SDIO_D2 => PC10  
SDIO_D3 => PC11  
SDIO_CMD => PD2  
SDIO_CLK => PC12  
SDIO_D0 => PC8  
SDIO_D1 => PC9  
TF_DET => PD3  
  
USB_USART1_TX => PA9  
USB_USART1_RX => PA10  
  
RESET_BTN => NRST (14)  
LED_D2 => PD12  
  
WINBOND_CS => PB12  
WINBOND_DO => PB14  
WINBOND_DI => PB15  
WINBOND_CLK => PB13  
  
EEPROM_A1 => GND  
EEPROM_A2 => GND  
EEPROM_SDA => PB11  
EEPROM_SCL => PB10  
  
P1_1_LCD_9_CSX => PD7  
P1_2_LCD_11_WRX => PD5  
P1_3_TOUCH_15_/CS => PB2  
P1_4_TOUCH_14_DIN => PE5  
P1_5_TOUCH_12_DOUT => PE4  
P1_6_TOUCH_16_DCLK => PB0  
P1_7_TOUCH_11_/PENIRQ => PB1  
P1_8_LCD_12_RDX => PD4  
P1_9 => GND  
P1_10 => 3.3V  
  
P2_1_LCD_15_RESX => PE6  
P2_2_LCD_10_DCX => PD13  
P2_3_LCD_26_DB9 => PD15  
P2_4_LCD_25_DB8 => PD14  
P2_5_LCD_28_DB11 => PD1  
P2_6_LCD_27_DB10 => PD0  
P2_7_LCD_30_DB13 => PE8  
P2_8_LCD_29_DB12 => PE7  
P2_9_LCD_32_DB15 => PE10  
P2_10_LCD_31_DB14 => PE9  

## Resources

- [ET4 Telegram Spanish Group Resources](https://drive.google.com/drive/folders/1bVusF9dMh1H7c2JM5ZWlbn2tWRGKsHre)  
- [ET4 Board and specs](https://es.aliexpress.com/item/4000571722465.html?spm=a2g0o.productlist.0.0.5c647634dDFWSV&algo_pvid=9a06cdcd-c1f2-45a0-adcf-36da50fefff7&algo_expid=9a06cdcd-c1f2-45a0-adcf-36da50fefff7-2&btsid=0ab6f83115911132482433653e39a1&ws_ab_test=searchweb0_0,searchweb201602_,searchweb201603_)
- [Bltouch installation](/docs/Tutorials/bltouch-en.md)
- [Change boot image](/docs/Tutorials/change-marlin-imgs-en.md)
- [Build Marlin (ES)](/docs/Tutorials/build-es.md)
- [EasyConfig.h](/Marlin/EasyConfig.h)
- [OpenBLT project](https://github.com/davidtgbe/openblt)

## Acknowledgements

- To the mates of the [Telegram Anet ET4 spanish group](https://t.me/anetet4esp), specially to @Solidnon, who lent his board for testing when the project was not even started.
- To @uwe and @mubes from Black Magic Probe team, and to Ebiroll (BMP/ESP32).
- To all contributors and testers of this branch and, specially, of Marlin master branch.


# Marlin 3D Printer Firmware

<p align="center"><img src="buildroot/share/pixmaps/logo/marlin-outrun-nf-500.png" height="250" alt="MarlinFirmware's logo" /></p>

<h1 align="center">Marlin 3D Printer Firmware</h1>

<p align="center">
    <a href="/LICENSE"><img alt="GPL-V3.0 License" src="https://img.shields.io/github/license/marlinfirmware/marlin.svg"></a>
    <a href="https://github.com/MarlinFirmware/Marlin/graphs/contributors"><img alt="Contributors" src="https://img.shields.io/github/contributors/marlinfirmware/marlin.svg"></a>
    <a href="https://github.com/MarlinFirmware/Marlin/releases"><img alt="Last Release Date" src="https://img.shields.io/github/release-date/MarlinFirmware/Marlin"></a>
    <a href="https://github.com/MarlinFirmware/Marlin/actions"><img alt="CI Status" src="https://github.com/MarlinFirmware/Marlin/actions/workflows/test-builds.yml/badge.svg"></a>
    <a href="https://github.com/sponsors/thinkyhead"><img alt="GitHub Sponsors" src="https://img.shields.io/github/sponsors/thinkyhead?color=db61a2"></a>
    <br />
    <a href="https://fosstodon.org/@marlinfirmware"><img alt="Follow MarlinFirmware on Mastodon" src="https://img.shields.io/mastodon/follow/109450200866020466?domain=https%3A%2F%2Ffosstodon.org&logoColor=%2300B&style=social"></a>
</p>

Additional documentation can be found at the [Marlin Home Page](https://marlinfw.org/).
Please test this firmware and let us know if it misbehaves in any way. Volunteers are standing by!

## Marlin 2.1 Bugfix Branch

__Not for production use. Use with caution!__

Marlin 2.1 takes this popular RepRap firmware to the next level by adding support for much faster 32-bit and ARM-based boards while improving support for 8-bit AVR boards. Read about Marlin's decision to use a "Hardware Abstraction Layer" below.

This branch is for patches to the latest 2.1.x release version. Periodically this branch will form the basis for the next minor 2.1.x release.

Download earlier versions of Marlin on the [Releases page](https://github.com/MarlinFirmware/Marlin/releases).

## Example Configurations

Before you can build Marlin for your machine you'll need a configuration for your specific hardware. Upon request, your vendor will be happy to provide you with the complete source code and configurations for your machine, but you'll need to get updated configuration files if you want to install a newer version of Marlin. Fortunately, Marlin users have contributed dozens of tested configurations to get you started. Visit the [MarlinFirmware/Configurations](https://github.com/MarlinFirmware/Configurations) repository to find the right configuration for your hardware.

## Building Marlin 2.1

To build and upload Marlin you will use one of these tools:

- The free [Visual Studio Code](https://code.visualstudio.com/download) using the [Auto Build Marlin](https://marlinfw.org/docs/basics/auto_build_marlin.html) extension.
- The free [Arduino IDE](https://www.arduino.cc/en/main/software) : See [Building Marlin with Arduino](https://marlinfw.org/docs/basics/install_arduino.html)

Marlin is optimized to build with the **PlatformIO IDE** extension for **Visual Studio Code**. You can still build Marlin with **Arduino IDE**, and we hope to improve the Arduino build experience, but at this time PlatformIO is the better choice.

## Hardware Abstraction Layer (HAL)

Marlin includes an abstraction layer to provide a common API for all the platforms it targets. This allows Marlin code to address the details of motion and user interface tasks at the lowest and highest levels with no system overhead, tying all events directly to the hardware clock.

Every new HAL opens up a world of hardware. At this time we need HALs for RP2040 and the Duet3D family of boards. A HAL that wraps an RTOS is an interesting concept we would can explore. Did you know that Marlin includes a Simulator that can run on Windows, macOS, and Linux? Join the Discord to help move these sub-projects forward!

## 8-Bit AVR Boards

A core tenet of this project is to keep supporting 8-bit AVR boards while also maintaining a single codebase that applies equally to all machines. We want casual hobbyists to benefit from the community's innovations as much as possible just as much as those with fancier machines. Plus, those old AVR-based machines are often the best for your testing and feedback!

### Supported Platforms

  Platform|MCU|Example Boards
  --------|---|-------
  [Arduino AVR](https://www.arduino.cc/)|ATmega|RAMPS, Melzi, RAMBo
  [Teensy++ 2.0](https://www.microchip.com/en-us/product/AT90USB1286)|AT90USB1286|Printrboard
  [Arduino Due](https://www.arduino.cc/en/Guide/ArduinoDue)|SAM3X8E|RAMPS-FD, RADDS, RAMPS4DUE
  [ESP32](https://github.com/espressif/arduino-esp32)|ESP32|FYSETC E4, E4d@BOX, MRR
  [LPC1768](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc1700-cortex-m3/512-kb-flash-64-kb-sram-ethernet-usb-lqfp100-package:LPC1768FBD100)|ARM® Cortex-M3|MKS SBASE, Re-ARM, Selena Compact
  [LPC1769](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/general-purpose-mcus/lpc1700-cortex-m3/512-kb-flash-64-kb-sram-ethernet-usb-lqfp100-package:LPC1769FBD100)|ARM® Cortex-M3|Smoothieboard, Azteeg X5 mini, TH3D EZBoard
  [STM32F103](https://www.st.com/en/microcontrollers-microprocessors/stm32f103.html)|ARM® Cortex-M3|Malyan M200, GTM32 Pro, MKS Robin, BTT SKR Mini
  [STM32F401](https://www.st.com/en/microcontrollers-microprocessors/stm32f401.html)|ARM® Cortex-M4|ARMED, Rumba32, SKR Pro, Lerdge, FYSETC S6, Artillery Ruby
  [STM32F7x6](https://www.st.com/en/microcontrollers-microprocessors/stm32f7x6.html)|ARM® Cortex-M7|The Borg, RemRam V1
  [STM32G0B1RET6](https://www.st.com/en/microcontrollers-microprocessors/stm32g0x1.html)|ARM® Cortex-M0+|BigTreeTech SKR mini E3 V3.0
  [STM32H743xIT6](https://www.st.com/en/microcontrollers-microprocessors/stm32h743-753.html)|ARM® Cortex-M7|BigTreeTech SKR V3.0, SKR EZ V3.0, SKR SE BX V2.0/V3.0
  [SAMD51P20A](https://www.adafruit.com/product/4064)|ARM® Cortex-M4|Adafruit Grand Central M4
  [Teensy 3.5](https://www.pjrc.com/store/teensy35.html)|ARM® Cortex-M4|
  [Teensy 3.6](https://www.pjrc.com/store/teensy36.html)|ARM® Cortex-M4|
  [Teensy 4.0](https://www.pjrc.com/store/teensy40.html)|ARM® Cortex-M7|
  [Teensy 4.1](https://www.pjrc.com/store/teensy41.html)|ARM® Cortex-M7|
  Linux Native|x86/ARM/etc.|Raspberry Pi

## Submitting Patches

Proposed patches should be submitted as a Pull Request against the ([bugfix-2.1.x](https://github.com/MarlinFirmware/Marlin/tree/bugfix-2.1.x)) branch.

- This branch is for fixing bugs and integrating any new features for the duration of the Marlin 2.1.x life-cycle.
- Follow the [Coding Standards](https://marlinfw.org/docs/development/coding_standards.html) to gain points with the maintainers.
- Please submit Feature Requests and Bug Reports to the [Issue Queue](https://github.com/MarlinFirmware/Marlin/issues/new/choose). Support resources are also listed there.
- Whenever you add new features, be sure to add tests to `buildroot/tests` and then run your tests locally, if possible.
  - It's optional: Running all the tests on Windows might take a long time, and they will run anyway on GitHub.
  - If you're running the tests on Linux (or on WSL with the code on a Linux volume) the speed is much faster.
  - You can use `make tests-all-local` or `make tests-single-local TEST_TARGET=...`.
  - If you prefer Docker you can use `make tests-all-local-docker` or `make tests-all-local-docker TEST_TARGET=...`.

## Marlin Support

The Issue Queue is reserved for Bug Reports and Feature Requests. To get help with configuration and troubleshooting, please use the following resources:

- [Marlin Documentation](https://marlinfw.org) - Official Marlin documentation
- [Marlin Discord](https://discord.gg/n5NJ59y) - Discuss issues with Marlin users and developers
- Facebook Group ["Marlin Firmware"](https://www.facebook.com/groups/1049718498464482/)
- RepRap.org [Marlin Forum](https://forums.reprap.org/list.php?415)
- Facebook Group ["Marlin Firmware for 3D Printers"](https://www.facebook.com/groups/3Dtechtalk/)
- [Marlin Configuration](https://www.youtube.com/results?search_query=marlin+configuration) on YouTube

## Contributors

Marlin is constantly improving thanks to a huge number of contributors from all over the world bringing their specialties and talents. Huge thanks are due to [all the contributors](https://github.com/MarlinFirmware/Marlin/graphs/contributors) who regularly patch up bugs, help direct traffic, and basically keep Marlin from falling apart. Marlin's continued existence would not be possible without them.

## Administration

Regular users can open and close their own issues, but only the administrators can do project-related things like add labels, merge changes, set milestones, and kick trolls. The current Marlin admin team consists of:

<table align="center">
<tr><td>Project Maintainer</td></tr>
<tr><td>

 🇺🇸  **Scott Lahteine**
       [@thinkyhead](https://github.com/thinkyhead)
       [<kbd>  Donate 💸  </kbd>](https://www.thinkyhead.com/donate-to-marlin)

</td><td>

 🇺🇸  **Roxanne Neufeld**
       [@Roxy-3D](https://github.com/Roxy-3D)

 🇺🇸  **Keith Bennett**
       [@thisiskeithb](https://github.com/thisiskeithb)
       [<kbd>  Donate 💸  </kbd>](https://github.com/sponsors/thisiskeithb)

 🇺🇸  **Jason Smith**
       [@sjasonsmith](https://github.com/sjasonsmith)

</td><td>

 🇧🇷  **Victor Oliveira**
       [@rhapsodyv](https://github.com/rhapsodyv)

 🇬🇧  **Chris Pepper**
       [@p3p](https://github.com/p3p)

🇳🇿  **Peter Ellens**
       [@ellensp](https://github.com/ellensp)
       [<kbd>  Donate 💸  </kbd>](https://ko-fi.com/ellensp)

</td><td>

 🇺🇸  **Bob Kuhn**
       [@Bob-the-Kuhn](https://github.com/Bob-the-Kuhn)

 🇳🇱  **Erik van der Zalm**
       [@ErikZalm](https://github.com/ErikZalm)
       [<kbd>  Donate 💸  </kbd>](https://flattr.com/submit/auto?user_id=ErikZalm&url=https://github.com/MarlinFirmware/Marlin&title=Marlin&language=&tags=github&category=software)

</td></tr>
</table>

## License

Marlin is published under the [GPL license](/LICENSE) because we believe in open development. The GPL comes with both rights and obligations. Whether you use Marlin firmware as the driver for your open or closed-source product, you must keep Marlin open, and you must provide your compatible Marlin source code to end users upon request. The most straightforward way to comply with the Marlin license is to make a fork of Marlin on Github, perform your modifications, and direct users to your modified fork.

While we can't prevent the use of this code in products (3D printers, CNC, etc.) that are closed source or crippled by a patent, we would prefer that you choose another firmware or, better yet, make your own.

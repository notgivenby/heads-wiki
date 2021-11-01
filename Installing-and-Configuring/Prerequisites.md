---
layout: default
title: Prerequisites for Heads
permalink: /Prerequisites
nav_order: 1
parent: Installing and configuring
---

Prerequisites for Heads
===

Required equipment
---

To install Heads on a physical device, you will need:

* Supported motherboard or laptop ([see below](#supported-devices))
* A heads compatible USB security dongle ([see below](#USB Security Dongles))
* A heads compatible storage device for your public GPG key (USB flash drive)

If your device requires external flashing ([see below](#supported-devices)),
 you will also need:

* [SPI Programmer](https://trmm.net/SPI_flash): ch341a programmer or raspberry
 pi or bus pirate (ch341a is recommended for new users and can be found almost
 [anywhere](https://www.amazon.com/s?k=ch341a+programmer).
* Wires and a clip SOIC8 to connect your programmer of choice to the board’s
 SPI flash chip(s).
  * The [Pomona 5250](https://www.pomonaelectronics.com/products/test-clips/soic-clip-8-pin)
   is suggested as it is high quality and easier to make contact with the pins.
* A second computer to flash from (Try to use a recommended operating system:
  Qubes or Debian 9 or Fedora 30)

Supported devices
---

Please see the current [heads source](https://github.com/osresearch/heads/tree/master/boards) for supported devices.

|Device| Board name|Firmware base|Requires external flashing| ME should be cleaned|Notes|
|--|--|--|:--:|:--:|--|
|Asus KGPE-D16|`kgpe-d16`|coreboot|X|||
|Dell R630|`r630`|linuxboot||X||
|Intel S2600wf|`s2600wf`|linuxboot||X||
|Lenovo Thinkpad T420|`t420`|coreboot|X|X|Legacy board config without HOTP verification|
|Lenovo Thinkpad T420|`t420-maximized`|coreboot|X|X|Maximized board config without HOTP verification|
|Lenovo Thinkpad T420|`t420-hotp-maximized`|coreboot|X|X|Maximized board config with HOTP verification.|
|Lenovo Thinkpad T430|`t430-flash`|coreboot|X|X|Legacy initial flashing board config producing internal flash environment to flash non-maximized legacy boards.|
|Lenovo Thinkpad T430|`t430-hotp-verification`|coreboot|X|X|Legacy board config with HOTP verification. Flashable through legacy t430-flash image.|
|Lenovo Thinkpad T430|`t430`|coreboot|X|X||Legacy board config without HOTP verification. Flashed internally through legacy t430-flash image.|
|Lenovo Thinkpad T430|`t430-maximized`|coreboot|X|X|Needs unlocked IFD on initial flash to be able to first internally upgrade this full rom through manual flashrom invocation, or external reprogramming with bottom and top roms. See notes below.|
|Lenovo Thinkpad T430|`t430-hotp-maximized`|coreboot|X|X|With HOTP verification. Needs unlocked IFD on initial flash to be able to first internally upgrade this full rom through manual flashrom invocation, or external reprogramming with bottom and top roms. See notes below.|
|Lenovo Thinkpad X220|`x220`|coreboot|X|X|Legacy board config without HOTP verification|
|Lenovo Thinkpad X220|`x220-maximized`|coreboot|X|X|Maximized board config without HOTP verification.|
|Lenovo Thinkpad X220|`x220-hotp-maximized`|coreboot|X|X|Maximized board config with HOTP verification.|
|Lenovo Thinkpad X230|`x230-flash`|coreboot|X|X|Legacy initial flashing board config producing internal flash environment to flash non-maximized legacy boards.|
|Lenovo Thinkpad X230|`x230-hotp-verification`|coreboot|X|X|Legacy board config with HOTP verification. Flashable through legacy x230-flash image.|
|Lenovo Thinkpad X230|`x230`|coreboot|X|X||Legacy board config with HOTP verification. Flashable through legacy x230-flash image.|
|Lenovo Thinkpad X230|`x230-hotp-maximized`|coreboot|X|X|Maximized board config with HOTP verification.|
|Lenovo Thinkpad X230|`x230-maximized`|coreboot|X|X|Maximized board config with HOTP verification.|
|Open Compute Project Leopard node|`leopard`|linuxboot|||
|Open Compute Project TiogaPass node|`tioga`|linuxboot||||
|Open Compute Project Winterfell node|`winterfell`|linuxboot||||
|Purism Librem 13 v2/v3|`librem_13v2`|coreboot||||
|Purism Librem 13 v4|`librem_13v4`|coreboot||||
|Purism Librem 15 v3|`librem_15v3`|coreboot||||
|Purism Librem 15 v4|`librem_15v4`|coreboot||||
|Purism Librem Mini|`librem_mini`|coreboot||||
|Purism Librem Mini v2|`librem_mini_v2`|coreboot||||
|Purism Librem 14|`librem_14`|coreboot||||

Legacy vs Maximized boards
---
Some history first on the historical x230-flash and x230 boards that initially created the Heads project.

Heads was initially developped on the x230 board. 
At that time, ME cleaning/neutering was not a thing. Then me_cleaner facilitated the task.
The X230 board, as all xx30 family boards do not have a single SPI flash, but two. 
On the bottom SPI flash chip lies the IFD, ME, GBE and some BIOS available space spanning. On the top SPI flash is the original BIOS region.
Original work done on Heads without ME cleaning led to the creation of a two phase flashing of the board. It required an original external flashing of the x230-flash ROM to the 4MiB top chip, which permitted to boot into a minimal BIOS from which the x230 board 12MiB ROM could be internally flashed through flashrom and Linux, fitting into the 4MiB SPI flash from x230-flash ROM. Botting into x230-flash launches a recovery shell which made possible to flash only the BIOS region from the x230 created ROM. This ROM image is incomplete, and flashing the whole 12MiB image would create a brick. A script made available through x230-flash was taking care of only flashing the BIOS region of the untouched IFD region, which permitted to flash the 7MiB defined BIOS region inside of the IFD descriptor, without touching ME, GBE or the IFD itself.

Then me_cleaner came to life, which permitted to clean ME in different ways. me_cleaner project grew mature, and eventually permitted, for xx20 and xx30 family, to not only clean ME, but neuter it. Neutering here means that not only ME was asked to deactivate itself, but most of the modules inside of it could be removed. For the xx20 family, it eventually meant that only BUP was required for the computer to function, while for the xx30, BUP and ROMP was necessary. This also meant that the sapce used by the ME kernel and libraries being deleted could be reused for other purposes. But that space being freed could never be took for granted. Unless the IFD descriptor was modified to reduce ME region to match freed space, coreboot CBFS region maximized to match freed ME available space. Doing so permitted the BIOS region (coreboot + Heads) to jump from a 7MiB available BIOS region in IFD descriptor to 11.5MiB on the x230 and xx30 boards. But no board configuration permitted to take advantage of that for numerous reasons, most of which being legal matters, with blobs being non redistributable.

The consequence of that is the multiple xx20 and xx30 boards now lying under Heads repository, and the complexity for newcomers to build Heads for the first time.

In short, legacy boards will produce ROMs that are incomplete by themselves; they do not contain a valid IFD descriptor and require internal and manual flashrom invocation from a script to inform flashrom to use the actual IFD defined BIOS region and flash that matching area only. Otherwise a non-booting system would result. A brick.

The maximized boards were created to produce fully valid and complete images for those boards. Blobs download/cleaning scripts were created for xx20 and xx30 platforms, which download ME blobs from the manufacturer, remove all the nasty bits reducing ME used space to the minimal and put resulting blob where it is needed from coreboot configuration to be integrated in the final produced ROM. A valid IFD descriptor is provided under the blob directory to match reduced ME size, giving the freed space to the BIOS region. A generated GBE blob is also provided in tree, required to have a functional e1000e ethernet interface, with an important limitation to be known from Heads users: the MAC address of maximized boards is fixed to DE:AD:C0:FF:EE. That is not so important for the majority of us connecting through wifi nowadays. But if a lot of Heads machines are living on the same LAN, or if privacy is needed through Ethernet connection, NetworkManager or other manual configuration will need to be applied to randomize/fixate Ethernet MAC address to desired value prior of connecting to a network.

Current Legacy boards
---
xx30-flash: 4MiB ROM images to be flashed internally through 1vyrain or Skulls. Unlocking IFD and cleaning/neutering ME still highly recommended prior of doing initial flash.

xx30: Baked 12MiB ROM images to be flashed internally through xxxx-flash flashed ROMs. Those ROMs can only be internally flashed from/to legacy boards configuration. Flashing a legacy ROM from a maximized ROM will result in a brick, since maximized boards produced roms will flash the whole combined opaque 12MiB ROM internally, overwriting IFD, ME and GBE with empty content.

xx20: Those ports (t420 and xx20 at time of writing) landed on Heads later in time and were historically produced by making required blobs available by applying scripts on SPI backups to extract required blobs. Consequently, those boards do not suffer from feature reduction as of now; they always took for granted that ME was neutered and IFD was unlocked. They still only flash internally the BIOS region, which was maximized to take advantage of 7.5MiB available SPI space for BIOS region, while not reflashing the whole SPI. 

xxxx-hotp-verification: Legacy, reduced versions of their hotp maximized counterpart. At the time of writing this, those board configuration will normally loose dropbear support, while xx30 versions will not have FBWhiptail anymore. That means that there is no framebuffer enforced graphical interface under Heads with background color queues notifying the end user of warning (yellow) or errors (red) contextual, graphical menus.

Current Maximized boards
---
xx30-maximized: Those board configuration produce 3 ROM images: One full 12MiB image containing reduced ME, maximized IFD BIOS region and GBE to be flashed internally, and two splitted out ROM images from the full image named bottom and top images, aimed to be externally flashed to their physical SPI counterparts 
If built locally, the builder has the option of extracting blobs from a backup image which will put ME and IFD binaries at the right place in the blobs directory which will be included into coreboot created full ROM image including Heads payload. There is a risk that ME will be bigger/smaller. In the case ME is bigger then expected, there is a chance that the flashed system will result in a brick. This is why me_cleaner instructions in this website strongly advise into upgrading to Lenovo 2.76 version prior of backuping the bottom SPI ROM, unlock IFD and clean that specific version of proprietary firmware. This is also why it is suggested to only backup your GBE to keep your MAC address for Ethernet and use the download script under blobs/xx30 to download and neuter verified version of Lenovo downloaded ME. Those do not enforce HOTP firmware verification against supported USB Security dongles and rely solely on TOTP to manually verify firmware integrity prior of each boot, that is: launching OTP application on smartphone to manually verify that the TOTP code produced on both screens of smartphone and laptop matches from smartphone scanned Qr code after first boot of Heads.

xxxx-hotp-maximized: Those board configuration are the same as prior maximized board configuration, but produce ROM images enforcing TOTP+HOTP for firmware verification on supported already bought and received USB Security dongles to be bounded with Heads.

Emulated devices
---

For further information, see [Emulating Heads](/Emulating-Heads/)

|Device| Board name|  Firmware base|
|--|--|--|
|QEMU development image|`qemu-coreboot-fbwhiptail`|coreboot|
|QEMU development image|`qemu-coreboot`|coreboot|
|QEMU development image|`qemu-linuxboot`|linuxboot|

USB Security Dongles (aka security token aka smartcard)
---

*NOTE* - Heads does **NOT** support FIDO2 or U2F authentication.  Be careful when
 purchasing to buy a compatible key.

 *NOTE* - HOTP is currently only supported with Librem devices and the ThinkPad
  x230 rom with HOTP support

|Manufacture|Model line|TOTP|HOTP|
|--|--|:--:|:--:|
|Yubico|[YubiKey 5 Series](https://www.yubico.com/products/yubikey-5-overview/)|X||
|Nitrokey|[Nitrokey Pro 2](https://www.nitrokey.com/#comparison)|X|X|
|Nitrokey|[Nitrokey Storage 2](https://www.nitrokey.com/#comparison)|X|X|
|Purism|[Librem Key](https://puri.sm/products/librem-key/)|X|X|
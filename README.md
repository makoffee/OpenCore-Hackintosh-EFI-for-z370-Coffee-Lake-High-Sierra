# OpenCore Hackintosh EFI for z370 Coffee Lake High Sierra
Intel i7 8700K / Asus Z370-Prime-A / Nvidia GTX 1080 / OpenCore High Sierra [SUCCESS]

Big thanks to [amrios](https://github.com/amrios) for his great starter EFI [project](https://github.com/amrios/z370-g_vanilla) I based most of this work on.  I appreciated his help so much I've decided to publicly post my EFI config in the hope that it's helpful to anyone else with a similar hardware build.  No promises but I will try to keep this up to date as I continue my hackintosh adventures.

# The Build
- Asus Z370 Prime-A
- Intel i7 8700K 
- Nvidia GTX 1080 Founders Edition
- Samsung SSD 960 EVO 500GB
- 32gb (4x8gb) G-Skill TridentZ F4-3000C14-8GTZR RGB DDR4 @ 3000 MHz

## Forward
### Why stay on High Sierra?
MacOS 10.13.6 "High Sierra" was the last version of MacOS to support Pascal based Nvidia GPUs via Nvidia's Web Drivers.

### On OpenCore support for High Sierra builds with Nvidia dGPUs
Anyone who has had the pleasure of trying to follow Dortania's OpenCore Install Guide will know the documentation regarding 'Legacy' builds on older versions of MacOS are somewhat limited, and after many hours of my own time, spent trying to follow them verbatium have come to realize that another approche is needed.

I feel that sharing knowledge and config.plists are the only way for the hackintosh community to support eachother.

### Why do this at all, I thought Apple was moving away from Intel with their own SOC?  Is hackintosh dead?
Good point.  I myself have recently purchased a new M1 macbook pro, and to my astonishment not all software has made the transition to nativly comiled code for Apple Silicon.  Furthermore Rosette emulation isn't working for 32bit applications and some productivity apps that are apart of my production workflow are not functional.  Nor is there any support for FireWire in the latest versions of MacOS.  *(Yes I still have drives and audio devices that use FireWire.)*  So in my infinite wisdom, having just formated my Clover based hackintosh, realized I may need to keep an older version of MacOS around to support my hardware and software a bit longer until Apple is able to address these issues with software updates.

# Getting started
### Bios settings

#### Disable:
- Boot  > **Fast Boot**
- Boot > **Secure Boot**
- Boot > CSM (Compatibility Support Module) > **CSM**
- Advanced > Onboard Devices > Configuration > **Serial/COM Port**
- Advanced > Onboard Devices > Configuration > **Parallel Port**
- Advanced > System Agent (SA) Configuration > **VT-d** 
- Advanced > Thunderbolt™ Configuration > **Thunderbolt**
- Advanced > Cpu Configuration > **Intel SGX**
- Advanced > PCH-FW Configuration > TPM > **Intel Platform Trust**

*Note: Turn off anything having todo with secure boot.  This is an EFI feature that's not compatible with Hackintosh because of Apples T2 secure rom.*

#### Enable:
- Boot > Secure Boot > OS type : **Other OS**
- Advanced > CPU Configuration > Intel Virtualization Technology > **VT-x **
- Advanced > CPU Configuration > **Hyper-Threading**
- Advanced > System Agent (SA) Configuration > **Above 4G decoding**
- Advanced > System Agent (SA) Configuration > Graphics Configuration > DVMT Pre-Allocated(iGPU Memory): **64MB**
- Advanced > PCH Storage Configuration > **SATA Mode: AHCI**
- Advanced > System Agent > Graphics Configuration > **Primary Display: PCIE**
- Advanced > System Agent > Graphics Configuration > **iGPU Multi-Monitor: Disabled** *(Not needed after WhateverGreen 1.3.9)*

*Other guides always mention the following, but I don't have them available in my BIOS revision so I've added these for those who need it:*

- Execute Disable Bit
- EHCI/XHCI Hand-off

### Note on clock settings, AVX and XMP
Start at stock speeds and work your way up to more agressive overclocks once you have a stable installation working.  I had a lot of trouble with AVX levels that were too low and caused errors.

## Dortania's OpenCore Install Guide
https://dortania.github.io/OpenCore-Install-Guide/

I know I've already thrown some shade toward's Dortania because of a lack of documented support for older versions of MacOS, however I must say that this guide is incredible and the amout of work that's gone into it is impressive.  However I must say this guide is meant to make you "get good noob", and it's targeted at users atempting more recent builds.  You will learn a lot and I highly recommend getting familiar with this guide.  

However for our purposes skip to the bit about [**"Creating the USB"**](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/). And then come back here once you have gotten to the " Adding The Base OpenCore Files" as we have everything you need here.

Now that you have created your bootable High Sierra USB stick copy the contents of this repo to your mounted EFI partition on your USB stick.

#### For reference we are using the following:

**OpenCorePKG v0.8.6**

### ACPI:
- SSDT-AWAC.aml
- SSDT-EC-USBX.aml
- SSDT-PLUG.aml
- SSDT-PMC.aml

### Drivers:
- [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)
- [OpenCanopy.efi](https://github.com/CeuiLiSA/Opencore-EFI/blob/master/EFI/OC/Drivers/OpenCanopy.efi)
- [OpenRuntime.efi](https://github.com/CeuiLiSA/Opencore-EFI/blob/master/EFI/OC/Drivers/OpenRuntime.efi)

### Kexts:
- [AppleALC.kext](https://github.com/acidanthera/AppleALC/releases)
- [IntelMausi.kext](https://github.com/acidanthera/IntelMausi/releases)
- [Lilu.kext](https://github.com/acidanthera/Lilu/releases)
- [USBMap.kext](https://github.com/corpnewt/USBMap) Mine is based on an z370G (you will want create your own map post install)
- [VirtualSMC.kext](https://github.com/acidanthera/VirtualSMC/releases)
- [WhateverGreen.kext](https://github.com/acidanthera/WhateverGreen/releases)
- [XHCI-unsupported.kext](https://github.com/johnlimabravo/XHCI-unsupported)

*It is advisable to grab the latest versions of the drivers, kexts, and spend some time learning how to set up your own manual builds for SSDTs via the guide. However you may have luck just using the provided versions in this repo to get through the install process.*

### Edit PlatformInfo in config.plist
Requirements: **GenSMBIOS**
https://github.com/corpnewt/GenSMBIOS

Here you will have to get your hands dirty a bit, and replace the following lines in config.plist with the SMBIOS info you generate with the GenSMBIOS scripts.  There's no getting around it, the serial numbers and ROM have to be unique and in the correct format.

**replace the following XML entries in config.plist:**

- PlatformInfo > Generic > SystemProductName  [iMac18,3 For High Sierra and older]
- PlatformInfo > Generic > SystemSerialNumber  [Serial from GenSMBIOS]
- PlatformInfo > Generic > MLB  [Board Serial from GenSMBIOS]
- PlatformInfo > Generic > SystemUUID [SmUUID from GenSMBIOS]
- PlatformInfo > Generic > ROM [See note from OpenCore Install Guide]

From OpenCore Install Guide
> We set Generic -> ROM to either an Apple ROM (dumped from a real Mac), your NIC MAC address, or any random MAC address (could be just 6 random bytes, for this guide we'll use 11223300 0000. After install follow the Fixing iServices (opens new window) page on how to find your real MAC Address)

That's it! Save your config.plist and you should be ready to atempt an installation.

#### Pro tip for installing from USB: 
**Try using a USB 2.0 port on the back of your motherboard.**  I spent DAYS trying to boot from usb 3.0 ports with no luck at all, untill I found a reddit post that pointed me in the right direction.  

Don't forget to do the Post install process from the OpenCore Install Guide, and install the Nvidia Webdrivers.  

Good luck!

# Lenovo-Ideapad-S340-Hackintosh-Catalina-OpenCore

Hey, looks like you came across my repository for this device. Looking to install macOS on it? Let's get started!

Notes: My CPU (Intel Core i3-8145U) is Whiskey Lake - however for our case it's the same as Coffee Lake, and will be referred as so.
This is written for OpenCore 0.5.7, I will update it when OpenCore 0.5.8 is released. New OpenCore releases happen on the Monday of the next month.

First, let's start with what doesn't work.
-
- HDMI Port (To be fixed, I haven't worked on it.)
- SD Card Reader (O2 Card Readers can be renamed, Sinetek's driver for Realtek cards hung at boot for me)
- DRM - This won't work any iGPU, because Intel's graphics cards don't support FairPlay.
- Intel WiFi. This **must** be replaced in order to use WiFi.

What does work:
- 
- Keyboard
- Trackpad, full gestures.
- Camera
- Graphics Acceleration
- CPU Power Management
- Battery
- Display Brightness and Brightness Keys (F11 and F12)
- Intel Bluetooth
- USB Ports
- Keyboard Backlight and Fan Management (Controlled by the BIOS)
- Sleep and Wake
- Audio and Internal Combo Port
- iMessage and FaceTime (Assuming you setup your SMBIOS correctly)
- ~~that annoying power button LED that flickers when battery is low~~

So, let's set it up!
-
First, here's the guide I followed.

[https://1revenger1.gitbook.io/laptop-guide/](https://1revenger1.gitbook.io/laptop-guide/)


*Create the USB and EFI Partition.*
- Use the `createinstallmedia` command from Apple, or [gibMacOS](https://github.com/corpnewt/gibMacOS) to make the installer. Mount the EFI Partition.
- Add the OpenCore files to it. Make it look exactly like below (delete files!)
- - -
- EFI
- - BOOTx64.efi
- OC
- - ACPI
- - Drivers
- - - APFSDriveLoader.efi
- - - HFSPlus.efi (Don't use vBoxHFS, it's slow)
- - - OpenRuntime.efi (Previously called FWRuntimeServices.efi)
- - Kexts
- - Tools
- - - OpenShell.efi (Optional - Usefull for accessing the EFI shell)
- - -

Great! Now that your directory tree looks like this, we need kexts. You can find these in the guide linked above.
Add these to OC/Kexts
- VirtualSMC.kext
- - SMCProcessor.kext
- - SMCBatteryManager.kext
- - SMCSuperIO.kext
- Lilu.kext
- WhateverGreen.kext
- AppleALC.kext
- VoodooPS2Controller.kext
- VoodooI2C.kext
- - VoodooI2CHID.kext 
- NoTouchID.kext

If you have working WiFi (a compatible WiFi Card that **isn't** a real Apple card, or Fenvi) add these: 
- AirportBrcmFixup.kext
- BrcmBluetoothInjector.kext
- - BrcmFirmwareData.kext
- - BrcmPatchRAM3.kext

They may be different for you card, so check here! 

[https://khronokernel-7.gitbook.io/wireless-buyers-guide/types-of-wireless-card/m2](https://khronokernel-7.gitbook.io/wireless-buyers-guide/types-of-wireless-card/m2)

---
Got your Kexts? Great, we need to add ACPI Patches to add functionality to the laptop. 


The ACPI Guide is covered here: 

[https://khronokernel.github.io/Getting-Started-With-ACPI/](https://khronokernel.github.io/Getting-Started-With-ACPI/)


- SSDT-GPI0 - Adds a hook for VoodooI2C controller (Required for trackpad support)
- SSDT-XOSI - Redirects _OSI to XOSI calls (Required for trackpad support)
- SSDT-PNLFCFL - SSDT-PNLF doesn't work on some CofeeLake laptops, SSDT-PNLFCFL will enable the backlight and brightness adjustment.
- SSDT-PLUG - plugin-type=1 for OpenCore, native CPU Power management.
- SSDT-HPET - used with a handful of ACPI Patches to fix IRQ conflicts in the DSDT. (We will generate this later!)
- SSDT-GPRW - Lets the laptop sleep and wake up! No more insomnia in macOS.
- SSDT-Q11Q12 - Redirects ACPI calls from the keyboard to macOS, to enable using F11 and F12 to adjust the brightness. Also requires two ACPI patches.
- SSDT-PSF13 - Changes the PrintScreen button from disable trackpad to F13 (Optional)
# OpenCore config.plist 

Grab the sample.plist file from the Docs folder and paste it to `OC` Folder. Rename it to `config.plist` 


Example: 

`EFI/OC/config.plist` 


Use [ProperTree](https://github.com/corpnewt/ProperTree) to edit the plist. 


Go to File -> OC Clean Snapshot. Point it at the OC folder. 


Note: To OC Snapshot: `CTRL + R` or `Command + R` and point it at the OC folder. It will automatically add your kexts, drivers, and ACPI patches to the config. 


For CoffeeLake, the entire config.plist is covered here: 

[https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/config.plist/coffee-lake.html](https://khronokernel.github.io/Opencore-Vanilla-Desktop-Guide/config.plist/coffee-lake.html) 


A few changes need to be made for this device.

ACPI -> Patch 

Dump your DSDT to check your EC device - it should be named `EC0`. If it ain't look here on how to rename different ones. **Make sure you dump your DSDT in Windows or Linux.** 

Use [SSDTTime](https://github.com/corpnewt/SSDTTime) to dump it. 

The complete ACPI guide: 

[https://khronokernel.github.io/Getting-Started-With-ACPI/Laptops/laptop-ec.html](https://khronokernel.github.io/Getting-Started-With-ACPI/Laptops/laptop-ec.html) 


**Rename EC0 to EC**
---

`Comment` = `Change EC0 to EC` [String] 

`Count` = `0` [Number] 

`Enabled` = `True` [Bool] 

`Find` = `4543305F` [Data] 

`Limit` = 0 [Number] 

`Replace` = `45435F5F` [Data] 


**Change _OSI to XOSI**
---

`Comment` = `Change _OSI to XOSI` [String] 

`Count` = `0` [Number] 

`Enabled` = `True` [Bool] 

`Find` = `5F4F5349` [Data] 

`Limit` = 0 [Number] 

`Replace` = `584F5349` [Data] 


**Change Method(_Q11,0,N) to XQ11**
---

`Comment` = `Change Method(_Q11,0,N) to XQ11` [String] 

`Count` = `0` [Number] 

`Enabled` = `True` [Bool] 

`Find` = `5F513131 00` [Data] 

`Limit` = 0 [Number] 

`Replace` = `58513131 00` [Data] 


**Change Method(_Q12,0,N) to XQ12**
---

`Comment` = `Change Method(_Q12,0,N) to XQ12` [String] 

`Count` = `0` [Number] 

`Enabled` = `True` [Bool] 

`Find` = `5F513132 00` [Data] 

`Limit` = 0 [Number] 

`Replace` = `58513132 00` [Data] 


---
**Wait!** You still aren't done with ACPI Patches. You need SSDT-HPET and the ACPI Patches. Use [SSDTTime](https://github.com/corpnewt/SSDTTime) on Windows or Linux to get them. 

Once you're done, look in the results folder inside SSDTTime and open the OpenCore config.plist, and copy the patches to your config.plist. Copy SSDT-HPET.aml to EFI/OC/ACPI. If it's named `SSDT-HPET.dsl`, you need to compile it. 

That's all for ACPI. And it's a lot. 


---
Under Booter -> Quirks 

`DevirtualizeMmio` **must** be set to FALSE 

`RebuildAppleMemoryMap` **must** be set to FALSE 

`SetupVirtualMap` **must** be set to TRUE 


Under DeviceProperties -> PciRoot(0x0)/Pci(0x2,0x0) 


For a UHD 620: 

`device-id` = `9B3E0000` [Data] 

`AAPL,ig-platform-id` = `00009B3E` [Data] 


For a UHD 630: 

Go to ark.intel.com and check your iGPU's device-id. If it's `0x3E9B`, you do not need a device-id. 

`device-id` = `9B3E0000` [Data] 

`AAPL,ig-platform-id` = `00009B3E` [Data] 


If you have a dedicated / discrete GPU, it is **not** compatible, so make sure to disable it! 


To properly add the AppleALC layout-id, we'll use DeviceProperties **instead of the boot-argument. If you have `alcid=XX` *remove it.***

My working alcid is 11, it goes as follows: 

DeviceProperties -> PciRoot(0x0)/Pci(0x1F,0x3) 

`layout-id` = `0B00000` [Data] 

In depth here: 

[https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/audio](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/audio) 


If you have a Broadcom BCM94350ZAE WiFi Card (DW1820a) you might be able to get it to work correctly. (mine does) 

Under DeviceProperties -> PciRoot(0x0)/Pci(0x1D,0x2)/Pci(0x0,0x0) 

`pci-aspm-default` = `0` [Number] 

This will disable power management for the card, and (in my case) it worked. 



# Fixing Apple iCloud, FaceTime, Messages, and other Apple Services.

Use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) to generate a SMBIOS, under PlatformInfo -> Generic. In details here: [https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/iservices](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/iservices) 

Make sure you do this correctly! If you get an Apple Support Customer Code, you have to call Apple to get it fixed. Also, **you will need an Ethernet/WiFi device named en0 and is built-in.**

# Fixing Audio, again!
You may notice if you have headphones / mic plugged into the Combo port, that waking from sleep completely breaks it. 

To fix it, add Codec Commander to OC/Kexts.  

[https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/](https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/) 

**Again, make sure to OC Snapshot when adding the kext.** 

Next, run `./install.sh` Inside the HDAFix Folder. 

Reboot. 


# Enabling GUI and Boot Chime

This is fairly easy, covered here: 

[https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/gui](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/gui) 


Copy the resources folder from [here](https://github.com/acidanthera/OcBinaryData) into `EFI/OC`
Example: `EFI/OC/Resources/` 

Grab OpenCanopy.efi from here. https://github.com/acidanthera/OpenCorePkg/releases
It's in the OC/Drivers folder. You may of deleted it earlier when setting up your EFI Folder. 

Add `OpenCanopy.efi` to `EFI/OC/Drivers` 

Example: `EFI/OC/Drivers/OpenCanopy.efi` 


**Do not forget to run ProperTree Snapshot (CTRL / Command + R) to add it to your config.plist**

In the config change the picker mode: 

-   Misc -> Boot -> `PickerMode` = `External` [String] 


Setup BootChime: 

Note: AudioDXE will delay your OpenCore load time slightly. 

Follow the guide linked above. Use these values, if they don't work, they may not be the same. 

`AudioCodec`= `0` [Number] 

`AudioDevice` = `PciRoot(0x0)/Pci(0x1F,0x3)` [String] 

`AudioOut`= `0` [Number] 


# Sources

- So much useful stuff in Corp's Repos. [https://github.com/corpnewt](https://github.com/corpnewt)
- Slavs insane brain power - [https://khronokernel.github.io/](https://khronokernel.github.io/)
- The entire acidanthera repo - [https://github.com/acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
- r/hackintosh discord server - [https://discord.gg/u8V7N5C](https://discord.gg/u8V7N5C "https://discord.gg/u8V7N5C")
- Tonymac - [https://www.tonymacx86.com/threads/guide-lenovo-ideapad-s340-laptop-on-catalina.288003/](https://www.tonymacx86.com/threads/guide-lenovo-ideapad-s340-laptop-on-catalina.288003/)
- My head and limited brain power
- Me having way to much free time
-  ~~OSX-Aptio-fix2drv-free-2000~~

# Additional Notes

- My WiFi doesn't work! How can I access it anyways?

~~Just buy a working WiFi card, simple.~~ 

This laptop has no Ethernet Port, but you aren't SOL.

If you have an Android Phone, you can use [HoRNDIS](https://joshuawise.com/horndis) (Yikes, outdated website.) Also, if you're on Catalina, you might need to run in terminal: 
`sudo mount -uw /`

If you have an iPhone, you might be able to use TetherMe, but I don't know much about it, so don't ask me.

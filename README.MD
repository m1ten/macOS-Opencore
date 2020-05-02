
  

  

  

# Lenovo-Ideapad-S340-Hackintosh-Catalina-OpenCore

  

  

Hey, looks like you came across my repository for this device. Looking to install macOS on it? Let's get started!

  

  

Notes: My CPU (Intel Core i3-8145U) is Whiskey Lake - however for our case as it's an 8th Gen CPU, we will follow the Coffee Lake guidelines.

This is written for OpenCore 0.5.7, I will update it when OpenCore 0.5.8 is released. New OpenCore releases happen on the Monday of the next month.

  
*****_April 18, 2020 Update_*****
- Moved VoodooInput kext locations to correct position on Kernel -> Add
- Added CPUFriend and CPUFriendDataProvider. Link on how to create: (Is disabled in config) [https://github.com/corpnewt/CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)
- Changed Kernel Quirks - You must disable CFG-Lock in BIOS if you want to keep the `AppleCpuPmCfgLock` off. This will also improve boot times. Source: [https://github.com/Hasodikis/Lenovo-Ideapad-s540-14IWL---Hackintosh](https://github.com/Hasodikis/Lenovo-Ideapad-s540-14IWL---Hackintosh) (Scroll down for tutorial)



*****_April 13, 2020 Update_*****

- Updated Guide

  

  

*****_April 12, 2020 Update_*****

  

- Removed CodecCommander

- Removed all audio sounds, except for Boot Chimes

- Added NVMeFix to increase battery life.

- Updated VoodooI2C and added VoodooInput

- - Trackpad dragging bug is fixed now

- Added a USB Map (necessary for USB 2.0 hot-plug)

- HDMI Support

  

---

  

Update History:

- macOS 10.14.4: *_Works, however feels less fluid than Catalina._*

- macOS 10.15.1: Successful - see notes for trackpad

- macOS 10.15.2: Successful - see notes for trackpad

- macOS 10.15.3: Successful - see notes for trackpad

- macOS 10.15.4: Successful - see notes for trackpad ****and HDMI****

- macOS 10.15.4 Supplemental Update: Successful - see notes for trackpad

  

---

  

Known Quirks

  

- FN + K will lower brightness.

- FN + P will increase brightness.

- Brightness adjustment isn't smooth.

- Trackpad can't be used to wake up from sleep.

- Power Button can't be used to wake up the machine until it is completely asleep. This won't be a problem, just use the keyboard to wake up the machine.

- Power button doesn't bring up the power menu.

- It has came to my attention HDMI has a yellow tint issue.

---

  

What does not work:  

- DRM - This won't work any iGPU, because Intel's graphics cards don't support FairPlay.

  

- Intel WiFi. This ****must**** be replaced in order to use WiFi.

  

- BCM94350ZAE Bluetooth - will look into.

  

  

---

  

What does work (Mostly everything you'd expect):

  

  

  -SD Card

- Keyboard

  

- Trackpad, full gestures

  

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

  

---

  

BIOS Settings

  

- Disk Mode: AHCI

  

- Intel Platform Trust Technology: Disabled

  

- Intel SGX Mode: Disabled

  

- Intel Virtualization: Optional, recommended to Disable

  

- Secure Boot: Disabled

- Disable CFG Lock
-

**Disable CFG Lock**
--

Fully power off the laptop. Enter the BIOS by pressing F2 at boot. Power off the laptop again. Quickly enter the following code:
```
     F1 → 1 → Q → A → Z
     
     F2 → 2 → W → S → X
     
     F3 → 3 → E → D → C
     
     F4 → 4 → R → F → V
     
     F5 → 5 → T → G → B
     
     F6 → 6 → Y → H → N
   ```

Power on the laptop while pressing F2 at boot. You'll see a lot of debug options. Don't mess with them. We only need to change one setting. 

**Advanced → Power & Performance → CPU-Power Management Control → CPU Lock Configuration → CFG Lock → Disabled**

Exit and Save, and CFG Lock will be disabled.


 # Start the process

First, here's the guide I followed.

  
[https://1revenger1.gitbook.io/laptop-guide/](https://1revenger1.gitbook.io/laptop-guide/)
In the near future, this guide will be deprecated, and instead use [https://dortania.github.io/vanilla-laptop-guide/](https://dortania.github.io/vanilla-laptop-guide/)
  

*__Create the USB and EFI Partition.__*

  

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

  

  

If you have working WiFi (a compatible WiFi Card that ******isn't****** a real Apple card, or Fenvi) add these:

  

- AirportBrcmFixup.kext

  

- BrcmBluetoothInjector.kext

  

- BrcmFirmwareData.kext

  

- BrcmPatchRAM3.kext

  

  

They may be different for you card, so check here!

  

  

[https://khronokernel-7.gitbook.io/wireless-buyers-guide/types-of-wireless-card/m2](https://khronokernel-7.gitbook.io/wireless-buyers-guide/types-of-wireless-card/m2)

  

  

---

  

Got your Kexts? Great, we need to add ACPI Patches to add functionality to the laptop.

  

  

  

The ACPI Guide is covered here:

  

  

[https://khronokernel.github.io/Getting-Started-With-ACPI/](https://khronokernel.github.io/Getting-Started-With-ACPI/)

  

  

  

- SSDT-GPI0 - Adds a hook for VoodooI2C controller (Required for trackpad support)

  

- SSDT-XOSI - Redirects _OSI to XOSI calls (Required for trackpad support)

  

- SSDT-PNLFCFL - SSDT-PNLF doesn't work on Whiskey Lake, SSDT-PNLFCFL is specifically for this and some Coffee lake laptops, it will enable the backlight and brightness adjustment. (But it won't make the brightness keys work!)

  

- SSDT-Q11Q12 - Redirects ACPI calls from the keyboard to macOS, to enable using F11 and F12 to adjust the brightness. Also requires two ACPI patches.

  

- SSDT-PLUG - plugin-type=1 for OpenCore, enables native CPU Power management. Note you have too look into your DSDT and change some values.

  

- SSDT-HPET - used with a handful of ACPI Patches to fix IRQ conflicts in the DSDT. (We will generate this later!)

  

- SSDT-GPRW - Lets the laptop sleep and wake up! No more insomnia in macOS.

  

  

  

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

  

  

Dump your DSDT to check your EC device - it should be named `EC0`. If it ain't look here on how to rename different ones. ******Make sure you dump your DSDT in Windows or Linux.******

  

  

Use [SSDTTime](https://github.com/corpnewt/SSDTTime) to dump it.

  

  

The complete ACPI guide:

  

  

[https://khronokernel.github.io/Getting-Started-With-ACPI/Laptops/laptop-ec.html](https://khronokernel.github.io/Getting-Started-With-ACPI/Laptops/laptop-ec.html)

  

  

  

******Rename EC0 to EC******

  

---

  

  

`Comment` = `Change EC0 to EC` [String]

  

  

`Count` = `0` [Number]

  

  

`Enabled` = `True` [Bool]

  

  

`Find` = `4543305F` [Data]

  

  

`Limit` = 0 [Number]

  

  

`Replace` = `45435F5F` [Data]

  

  

  

******Change _OSI to XOSI******

  

---

  

  

`Comment` = `Change _OSI to XOSI` [String]

  

  

`Count` = `0` [Number]

  

  

`Enabled` = `True` [Bool]

  

  

`Find` = `5F4F5349` [Data]

  

  

`Limit` = 0 [Number]

  

  

`Replace` = `584F5349` [Data]

  

  

  

******Change Method(_Q11,0,N) to XQ11******

  

---

  

  

`Comment` = `Change Method(_Q11,0,N) to XQ11` [String]

  

  

`Count` = `0` [Number]

  

  

`Enabled` = `True` [Bool]

  

  

`Find` = `5F513131 00` [Data]

  

  

`Limit` = 0 [Number]

  

  

`Replace` = `58513131 00` [Data]

  

  

  

******Change Method(_Q12,0,N) to XQ12******

  

---

  

  

`Comment` = `Change Method(_Q12,0,N) to XQ12` [String]

  

  

`Count` = `0` [Number]

  

  

`Enabled` = `True` [Bool]

  

  

`Find` = `5F513132 00` [Data]

  

  

`Limit` = 0 [Number]

  

  

`Replace` = `58513132 00` [Data]

  

  

  

---

  

******Wait!****** You still aren't done with ACPI Patches. You need SSDT-HPET and the ACPI Patches. Use [SSDTTime](https://github.com/corpnewt/SSDTTime) on Windows or Linux to get them.

  

  

Once you're done, look in the results folder inside SSDTTime and open the OpenCore config.plist, and copy the patches to your config.plist. Copy SSDT-HPET.aml to EFI/OC/ACPI. If it's named `SSDT-HPET.dsl`, you need to compile it.

  

  

That's all for ACPI. And it's a lot.

  

  

  

---

  

Under Booter -> Quirks

  

  

`DevirtualizeMmio` ******must****** be set to FALSE

  

  

`RebuildAppleMemoryMap` ******must****** be set to FALSE

  

  

`SetupVirtualMap` ******must****** be set to TRUE

  

  

  

Under DeviceProperties -> PciRoot(0x0)/Pci(0x2,0x0)

  

For a UHD 620 / UHD 630 on WhiskeyLake:

  

`AAPL,ig-platform-id` = `0900A53E` [Data]

`device-id` = `A53E0000` [Data]

  

If you have a dedicated / discrete GPU, it is ******not****** compatible, so make sure to disable it! Use an SSDT to fully disable it, or use the boot-argument `-wegnoegpu` to hide it from macOS. You can also check the DeviceProperties equivalent in the WhateverGreen Faq.

  

To properly add the AppleALC layout-id, we'll use DeviceProperties ******instead of the boot-argument. If you have****  `alcid=XX`  ***_**_remove it._**_*****

  

  

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

  

  

Use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) to generate a SMBIOS, under PlatformInfo -> Generic. Use `MacBookPro14,1` if you plan to use HDMI. In details here: [https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/iservices](https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/post-install/post-install/iservices)

  

  

Make sure you do this correctly! If you get an Apple Support Customer Code, you have to call Apple to get it fixed. Also, ******you will need an Ethernet/WiFi device named en0 and is built-in.******

  

  

# Fixing Audio, again!

  

You may notice if you have headphones / mic plugged into the Combo port, that waking from sleep completely breaks it.

  

  

Run `./install.sh` Inside the HDAFix Folder. Gatekeeper will deny it so you must go to Settings -> Security to allow ALCPlugFix and hdafix to run. AppleALC will take care of the rest.

  

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

  

  

  

******Do not forget to run OC Snapshot (CTRL / Command + R) to add it to your config.plist******

  

  

In the config change the picker mode:

  

  

- Misc -> Boot -> `PickerMode` = `External` [String]

  

  

  

Setup BootChime:

  

  

Note: AudioDXE will delay your OpenCore load time slightly.

  

  

Follow the guide linked above. Use these values, if they don't work, they may not be the same.

  

  

`AudioCodec`= `0` [Number]

  

  

`AudioDevice` = `PciRoot(0x0)/Pci(0x1F,0x3)` [String]

  

  

`AudioOut`= `0` [Number]

  

  

# HDMI Tutorial

  

  

![HDMI Working on Core i3-8145U Model](https://i.imgur.com/eHeHHCK.jpg)

  

- Notes:

- - HDMI hot-plug may take multiple attempts before initiating in rapid secession.

- - 10.15.4 makes the port a lot less reliable, ****avoid the update**** by using the `ChangeUpdates.txt` file in the tools folder to completely ignore the update. With this update, the HDMI port won't initialize on boot or when waking up the computer.

- - Be ready to change your SMBIOS data.

  

****Setting up the config.plist****

  

- Use GenSMBIOS to generate a SMBIOS for `MacBookPro14,1`

- *_Change GPU to Iris 655 to match SMBIOS:_*

- - Set `device-id` to `0x3EA50009`, `0900A53E` Hexswapped for DeviceProperties

- - Set `AAPL,ig-platform-id` to `0x3EA58086`, `3EA50009` Hexswapped for DeviceProperties

- Use Hackintool to generate a framebuffer patch using these settings:

  

- Pre-Notes: You mileage may vary

- - LVDS is for the eDP Internal Display.

- - As you can see, my HDMI port is Index 2 (Port 0x06) and requires Bus ID 0x02 to function. Setting the Pipe to 18 can sometimes help hot-plug issues. However, it's not perfect and may require multiple attempts.

  

![Hackintool Connectors](https://i.imgur.com/VKSf5FD.png)

  

  

Full guide: https://www.tonymacx86.com/threads/guide-general-framebuffer-patching-guide-hdmi-black-screen-problem.269149/

  

  

****Tip:**** To find your HDMI Index - Set each connector to -1 one at a time,

when you plug in your HDMI port it will kernel panic.

  

For example, my HDMI Port Index is 2, when set to -1 it will kernel panic.

  

That's how you can identify your port, instead of guessing.

  
# SD Card

O2 Readers will need to be renamed (see tonymac post)

Realtek cards have support now

Use cholonam's fork of Sinteks driver

https://github.com/cholonam/Sinetek-rtsx/releases

  

# USB Mapping

  

After you setup your HDMI port, your USB ports should also be setup correctly. In my case - USB 3.0 worked, however USB 2.0 hot-plug was completely messed up. No worries, fixing USB is really easy.

  

  

Summary: Plug in a USB 2.0 device to every USB port, set the ports that light up green as USB 2, plug a USB 3.0 device, set the types as USB 3.0.

  

Remove Unused Connectors by highlighting and pressing the Remove button.

  

Add the USBPorts.kext to EFI/OC/Kexts/ and use OC Snapshot in ProperTree.

  

The guide isn't perfect, but it will give you a general idea on what to do.

[https://www.tonymacx86.com/threads/the-new-beginners-guide-to-usb-port-configuration.286553/](https://github.com/cholonam/Sinetek-rtsx/releases)

# Additional Notes and Troubleshooting

  

  

******The trackpad isn't working******

  

  

I'm not all sure about this one, as mine just, *__works__*, but you can check here to make sure everything is correct. Note that my exact model has an I2CHID Trackpad, your's could be different.

  

  

[https://1revenger1.gitbook.io/laptop-guide/prepare-install-macos/setting-up-input-devices](https://1revenger1.gitbook.io/laptop-guide/prepare-install-macos/setting-up-input-devices)

  

****IF YOU UPDATED AND IT ISN'T WORKING*****

  

If it *__was__* working and stopped after an update, running `sudo kextcache -i /` and rebooting will fix it.

  

  

  

******My WiFi doesn't work! What are other options?******

  

  

~~Just buy a working WiFi card, simple.~~

  

  

This laptop has no Ethernet Port, but you aren't SOL.

  

  

- If you have an iPhone, you might be able to use TetherMe, but I don't know much about it, so don't ask me.

  

  

- If you have an Android Phone, you can use HoRNDIS.

  

  

Inject it through OpenCore instead of using the installer, as the installer installs the kext to Library/Extensions.

  

  

Copy HoRNDIS.kext from Tools/HoRNDIS.kext to EFI/OC/Kext/HoRNDIS.kext. OC Snapshot with Propertree.

  

  

Also, I would be *__highly against__* using USB WiFi in macOS, I would *__highly__* recommend using a proper WiFi card for this.

  

  

******macOS doesn't see my disk******

  

  

Make sure that your storage settings is set to AHCI instead of RAID.

  

  

*****_macOS 10.15.4 makes the HDMI port a whole lot less reliable, read through the HDMI tutorial for more information._*****

# To Do

  

- HDMI Port hot-plug reliability

- Better Sleep function through ACPI patches. (Note when you close the laptop display it still takes a while to actually sleep. Wake up times are acceptable)

- Attempt to get the Realtek SD Card Reader to function. (Sineteks driver hung the system at `gIOConsoleUsers,` prevented the iGPU from initializing. Will look into)

- Add pictures to my guide to make it prettier.

  

# Sources

  

  

- So much useful stuff in Corp's Repos. [https://github.com/corpnewt](https://github.com/corpnewt)

  

- Slavs insane brain power - [https://khronokernel.github.io/](https://khronokernel.github.io/)

  

- The entire acidanthera repo - [https://github.com/acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)

  

- r/hackintosh discord server - [https://discord.gg/u8V7N5C](https://discord.gg/u8V7N5C "https://discord.gg/u8V7N5C")

  

- Tonymac - [https://www.tonymacx86.com/threads/guide-lenovo-ideapad-s340-laptop-on-catalina.288003/](https://www.tonymacx86.com/threads/guide-lenovo-ideapad-s340-laptop-on-catalina.288003/)

  

- Me staying up until 7am messing with the HDMI port.

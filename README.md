# Dell G15 5510 Hackintosh EFI
Hackintosh EFI for Dell G15 5510
---

### Requirements:

Made for Intel Core i7-10870H CPU, other 10th gens will probably work. 11th gen CPUs __WILL NOT WORK__.

---
### How to set up:

The dGPU needs to be disabled. My laptop has an RTX 3060 GPU. The ACPI path might be different with other cards, but if it's Nvidia it's likely the same. Replace the ACPI path with yours in SSDT-NoHybGfx.aml.   

If you have more than 16 GBs of RAM, you don't need to do anything special, since DisableIoMapperMapping has to be enabled on every system. If it's not enabled it can cause problems like graphical glitches and kernel panics.

You'll need to generate new SMBIOS with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). Use MacBookPro16,4 smbios.   
> The Type part gets copied to Generic -> SystemProductName.   
The Serial part gets copied to Generic -> SystemSerialNumber.   
The Board Serial part gets copied to Generic -> MLB.   
The SmUUID part gets copied to Generic -> SystemUUID.

You'll probably need to recreate some ACPI patches:
- DMAR.aml - **You 100% need to recreate this!** It needs to be recreated every time you change a hardware (Fix DMAR)
- SSDT-HPET.aml (FixHPET)

Recreate them using [SSDTTime](https://github.com/corpnewt/SSDTTime). Download, start, then first choose *Dump the current system's ACPI tables*. After that, choose the options in the brackets found in the list above! Then copy the files to EFI/OC/ACPI.

After installing macOS, set up [ComboJack](https://github.com/hackintosh-stuff/ComboJack).

If you have any questions, first read through [Dortaina's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). Also, you need to create a macOS Installer, and for that, refer to the same guide.   
If you still have issues, don't hesitate to open an issue here.

---
### Fixes:

If you don't have a thunderbolt controller, remove SSDT-TB3.aml from ACPI folder, and remove its entry from config.plist > ACPI > Add. It should be the 10th entry.

If you have an unsupported NVMe (for example SK Hynix PC711; you'll know because it causes a panic), add SSDT-DISABLE-NVME-SLOTX.aml (where X is the NVMe slot which the unsupported drive is in) from the Extras/NVMe disable directory here to EFI > OC > ACPI and copy the corresponding Add value from the included patches.plist file in the directory to config.plist > ACPI > Add. It will fully disable that drive in macOS. You 100% won't be able to use it in macOS!

If your trackpad doesn't seem to work or even get detected, add the .aml files found in the Extras/Trackpad fix directory to EFI > OC > ACPI, and copy the Add and Patch values from the included patches.plist file in the directory to your config.plist file (ACPI > Add and ACPI > Patch). (Trust me. This was hard to figure out.)

If you want to use AOAC (Low Power S0 Idle), remove these SSDTs from ACPI folder and config.plist > ACPI > Add: SSDT-S3-ENABLE.aml and SSDT-NameS0-disable.aml and remove the rename _S0 to XS0 patch from config.plist > ACPI > Patch. Then add the SSDTs from Extras/AOAC sleep fix to OC/ACPI and add the values from Extras/AOAC sleep fix/patches.plist to your config.plist.   
This sleep has a problem in macOS: It can only be woken up by USB devices. The built-in keyboard, opening the lid, and the power button can't wake it up for some reason.

If you notice some Dell related tables not loading on startup, you can add the ACPI tables from Extras/Native ACPI fixes to OC > ACPI and add the patches.plist entries to your config.plist. This will fix loading the tables but it has no effect on how the system runs.

If you have long boot times and no graphics acceleration after boot, remove the igfxfw property from config.plist > DeviceProperties > PciRoot(0x0)/Pci(0x2,0x0). This will disable Apple GuC Firmware loading on the iGPU and your system should boot normally, but you will have less performance and DRM playback will break!

There are tools in this EFI for unlocking CFG lock (MSR 0xE2 Register write). If you don't know what this is all about, you probably shouldn't do that. It doesn't make much difference, anyways.

__Remember to change config_update.plist every time you change something in config.plist!__

---
### How to update macOS:

**IMPORTANT: if you make any changes in config.plist, also make them in config_update.plist!**

For minor updates, rename config.plist to config_normal.plist (or anything), then rename config_update.plist to config.plist. Then start the update process from System Settings.

What this does is that it disables Apple Secure Boot and BlueToolFixup.kext, as these are known to cause issues while updating macOS.

When there's a big update (like the upcoming Sonoma to Sequoia update), make sure to download the latest EFI from here (or update the kexts manually, you should do this regularly), then do the steps stated above, then without restarting (because of AirportItlwm having a different kext for every major release) start the update process. It will restart automatically.

After an update you can (and should) rename the now config_normal.plist to config.plist then restart.

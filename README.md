# Dell G15 5510 Hackintosh EFI
Hackintosh EFI for Dell G15 5510
---

### Requirements:

Made for Intel Core i7-10870H CPU, other 10th gens will probably work. 11th gen CPUs __WILL NOT WORK__.

---
### How to set up:

The dGPU needs to be disabled. My laptop has an RTX 3060 GPU. The ACPI path might be different with other cards. Replace the ACPI path with yours in SSDT-dGPU-Off.aml.   

If you have more than 16 GBs of RAM, enable the DisableIoMapperMapping quirk (**Not DisableIoMapper!**) in config.plist > Kernel > Quirks

You'll need to generate new SMBIOS with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). Use MacBookPro16,4 smbios.   
> The Type part gets copied to Generic -> SystemProductName.   
The Serial part gets copied to Generic -> SystemSerialNumber.   
The Board Serial part gets copied to Generic -> MLB.   
The SmUUID part gets copied to Generic -> SystemUUID.

You'll probably need to recreate some ACPI patches:
- DMAR.aml - **You 100% need to recreate this!** It needs to be recreated every time you change a hardware (Fix DMAR)
- SSDT-HPET.aml (FixHPET)
- SSDT-XOSI.aml (XOSI)

Recreate them using [SSDTTime](https://github.com/corpnewt/SSDTTime). Download, start, then first choose *Dump the current system's ACPI tables*. After that, choose the options in the brackets found in the list above! Then copy the files to EFI/OC/ACPI.

If you don't have a thunderbolt controller, remove SSDT-TB3.aml from ACPI folder, and remove its entry from config.plist > ACPI > Add. It should be the 10th entry.

If you have an unsupported NVMe (for example SK Hynix PC711; you'll know because it causes a panic), add SSDT-DISABLE-NVME-SLOTX.aml (where X is the NVMe slot which the unsupported drive is in) from the Extras/NVMe disable directory here to EFI > OC > ACPI and copy the corresponding Add value from the included patches.plist file in the directory to config.plist > ACPI > Add. It will fully disable that drive in macOS. You 100% won't be able to use it in macOS!

After installing macOS, set up [ComboJack](https://github.com/hackintosh-stuff/ComboJack).

If you have any questions, first read through [Dortaina's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). Also, you need to create a macOS Installer, and for that, refer to the same guide.   
If you still have issues, don't hesitate to open an issue here.

---
### Fixes:

If your trackpad doesn't seem to work or even get detected, add the .aml files found in the Extras/Trackpad fix directory to EFI > OC > ACPI, and copy the Add and Patch values from the included patches.plist file in the directory to your config.plist file (ACPI > Add and ACPI > Patch). (Trust me. This was hard to figure out.)

---
### How to update macOS:

**IMPORTANT: if you make any changes in config.plist, also make them in config_update.plist!**

For minor updates, rename config.plist to config_normal.plist (or anything), then rename config_update.plist to config.plist. Then start the update process from System Settings.

What this does is that it disables Apple Secure Boot and BlueToolFixup.kext, as these are known to cause issues while updating macOS.

When there's a big update (like the upcoming Sonoma to Sequoia update), make sure to download the latest EFI from here (or update the kexts manually, you should do this regularly), then do the steps stated above, then without restarting (because of AirportItlwm having a different kext for every major release) start the update process. It will restart automatically.

After an update you can (and should) rename the now config_normal.plist to config.plist then restart.

# Dell G15 5510 Hackintosh EFI
Hackintosh EFI for Dell G15 5510
---

### Requirements:

Made for Intel Core i7-10870H CPU, other 10th gens will probably work. 11th gen CPUs __WILL NOT WORK__.

---
### How to set up:

The dGPU needs to be disabled. My laptop has an RTX 3060 GPU. The ACPI path might be different with other cards. Replace the ACPI path with yours in SSDT-dGPU-Off.aml.   

If you have more than 16 GBs of RAM, enable the DisableIoMapperMapping quirk in config.plist > Kernel > Quirks

You'll need to generate new SMBIOS with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). Use MacBookPro16,4 smbios.   
> The Type part gets copied to Generic -> SystemProductName.   
The Serial part gets copied to Generic -> SystemSerialNumber.   
The Board Serial part gets copied to Generic -> MLB.   
The SmUUID part gets copied to Generic -> SystemUUID.

You'll probably need to recreate some ACPI patches:
- DMAR.aml
- SSDT-HPET.aml
- SSDT-XOSI.aml

Recreate them using [SSDTTime](https://github.com/corpnewt/SSDTTime).

If you don't have a thunderbolt controller, remove SSDT-TB3.aml from ACPI folder, and remove its entry from config.plist > ACPI > Add. It should be the 10th entry.

After installing macOS, set up [ComboJack](https://github.com/hackintosh-stuff/ComboJack).

If you have any questions, first read through [Dortaina's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). Also, you need to create a macOS Installer, and for that, refer to the same guide.   
If you still have issues, don't hesitate to open an issue here.

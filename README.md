# uki-automation-dracut
Config / Automation for Unified Kernel Images (UKIs) directly booted from UEFI menu with dracut and efibootmgr with a normal 
and an optional fallback image per kernel and Secure Boot enabled.

I use this with my Void Linux install on my laptop.
These scripts will create _signed_ (for Secure Boot) bootable efi files and boot-entries in the UEFI menu of your motherboard/laptop. No bootloader needed!

The scripts here are automatically used by `xbps-install` / `xbps-remove `and `vkpurge`.

Some notes:
- **Use at own risk**. Read closely and adjust to your needs.
- The easiest way to setup Secure Boot in te first place, is via `sbctl`  - install it. See [Arch wiki](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot) section on sbctl.
- For Secure Boot to work with dracut, you will need package `sbsigntool` installed. 
- Anything you put in `/etc/dracut.conf` or in conf files in `/etc/dracut.conf.d` will **always** be parsed by dracut. The reason to make 2 seperate configs in `/etc/dracut` is to have different configs for _normal_ and _fallback_ image that are independent from Void updates.
- These files will install a normal bootable efi file **and** a fallback bootalble efi file per kernel (2 per kernel). If you, for instance  installed `linux` and `linux-lts` kernel packages, you will end up with **4** bootable efi files (2 normal, 2 fallback).
- If a minor update of a kernel is installed, these scripts will remove the old files and UEFI entries and replace it with the new ones. Only with a new major version (for instance 6.4 becomes 6.5) then new files and entries will be created.
- You still need to clean old kernels from time to time with `vkpurge`. The remove-scripts here are used by vkpurge.
- The safety mechanism for efibootmgr conf files (which is to enable it in a file in `/etc/default` in the Void packaged files) **is removed** here to make it completely independent from Void  installed files in these directories.
- Before this automation works as intended you have to manually set your preferred order with `efibootmgt -o x,y,z`.
- You will need an efi-stub file, which can be installed via the package `gummiboot-efistub`.
- Dracut needs `objcopy` to build the efi files, this is provided by package `binutils`.
- My efi partition is mounted at `/boot`. You will have te create `EFI/Linux` directories beforehand.
- These files assume you have installed `plymouth` and microcode update-files for your kernel: `intel-ucode` if you have an Intel CPU.

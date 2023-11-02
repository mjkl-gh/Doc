``# Decrypt LUKS volumes with a TPM on Fedora 35+

This guide allows you to use the [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module) on your computer to decrypt your [LUKS](https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup) encrypted volumes. If you are worried about a [cold boot attack](https://en.wikipedia.org/wiki/Cold_boot_attack) on your hardware please ***DO NOT*** use this guide with your root volume!

#### Preflight Checks

Verify that you have a TPM in your computer:
```
# systemd-cryptenroll --tpm2-device=list
PATH        DEVICE     DRIVER 
/dev/tpmrm0 IFX0785:00 tpm_tis
```

***Note:*** If you have more than one TPM in your computer you will need to change `--tpm2-device=auto` to the exact TPM you want to use: `--tpm2-device=/dev/tpmrm0` and `rd.luks.options=tpm2-device=/dev/tpmrm0` in `GRUB_CMDLINE_LINUX`.

Verify that you are booted into SecureBoot.
```
# mokutil --sb-state
SecureBoot disabled
```

If you see that it is disabled you will need to enable it in the BIOS. You ***should*** enable SecureBoot before you start.

***Note:*** Enabling SecureBoot will cause third party kernel modules (such as NVIDIA drivers) to fail to load. You can work around this by using something like [this](https://github.com/larsks/akmod-sign-modules) to automatically sign the drivers built by akmod.

#### Find your LUKS encrypted volumes

```
# blkid -t TYPE=crypto_LUKS
/dev/nvme0n1p6: UUID="cad900c9-6937-47fd-bfed-c8fcbc71be3e" TYPE="crypto_LUKS" PARTLABEL="t15_system" PARTUUID="6b4aba4d-76da-4758-a357-d5f759b97909"
```

#### Enroll your encrypted volumes

```
# systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=0+2+4+7 /dev/nvme0n1p6
```
This will ask for your volume's passphrase. If you'd like to automate this in a script you may set the `PASSWORD` environment variable to your LUKS passphrase.

When setting `--tpm2-pcrs=0+2+4+7` the following items are these are validated at boot time:

```
0: System firmware executable
2: Kernel
4: Bootloader
7: Secure boot state
```

PCR 0,2,4,7 verifies the firmware, kernel, and bootloader before releasing the decryption key. If you are using PCR 2 and multiple kernels you will need to enroll a key for each kernel. If you have updated the firmware, kernel, or bootloader then auto volume decryption on your next reboot will fail. As long as you have a password set on your LUKS volumes you will be prompted to have to enter it to decrypt them and you will need to wipe the old key and enroll a new key if anything changes.

#### Ubuntu 
On ubuntu some extra steps must be taken to ensure systemd-cryptenroll has the correct functions set 

clone and run the scripts from:

https://github.com/wmcelderry/systemd_with_tpm2

and to enable to fallback to passphrase when the kernel was updated, make sure to check:

https://github.com/wmcelderry/systemd_with_tpm2/pull/7

##### Remove all TPM2 keys and enroll a new key

```
systemd-cryptenroll /dev/nvme0n1p6 --wipe-slot=tpm2 --tpm2-device=auto --tpm2-pcrs=0,2,4,7
```

#### Edit /etc/crypttab

Add `tpm2-device=auto,discard` to the end of each LUKS device line in `/etc/crypttab`
```
# cat /etc/crypttab
luks-cad900c9-6937-47fd-bfed-c8fcbc71be3e UUID=cad900c9-6937-47fd-bfed-c8fcbc71be3e none tpm2-device=auto,discard
```

##### Edit /etc/default/grub

Edit `/etc/default/grub` and add `rd.luks.options=tpm2-device=auto` to `GRUB_CMDLINE_LINUX`.

```
GRUB_CMDLINE_LINUX="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1 rd.luks.uuid=luks-014aa5a6-a007-11ec-a054-7c10c93c41b1 rd.luks.uuid=luks-0e9e99f6-a007-11ec-8130-7c10c93c41b1 rd.luks.options=tpm2-device=auto rhgb quiet rd.driver.blacklist=nouveau modprobe.blacklist=nouveau nvidia-drm.modeset=1"
```

#### Ubuntu initramfs 
Ubuntu currently doesnt support tpm2 devices in the initramfs

##### Add TPM2 support to dracut

Work around [this BZ](https://bugzilla.redhat.com/show_bug.cgi?id=1976462) by creating `/etc/dracut.conf.d/tss2.conf` and adding this line to it:

```
install_optional_items+=" /usr/lib64/libtss2* /usr/lib64/libfido2.so.* "
```

and then run `dracut -f` to rebuild the initrd. This should not be needed for Fedora 36+ as it is fixed in [Dracut 056](https://koji.fedoraproject.org/koji/buildinfo?buildID=1929112).


##### Recovery Key Enrollment (optional)

If you have a safe place to store a recovery key you can generate and add one for each LUKS volume. It will show the recovery key phrase on screen and generate a QR code you may scan off screen.

```
systemd-cryptenroll --recovery-key /dev/nvme0n1p6
```

##### Verify and reboot!

Verify that you have the TPM added to the encrypted volumes:
```
# systemd-cryptenroll /dev/nvme0n1p3
SLOT TYPE
   0 password
   1 tpm2
   2 recovery
# systemd-cryptenroll /dev/nvme1n1p1
SLOT TYPE
   0 password
   1 tpm2
   2 recovery
```

and now you can reboot and your TPM should unlock your encrypted drives!

Source:

https://gist.github.com/jdoss/777e8b52c8d88eb87467935769c98a95
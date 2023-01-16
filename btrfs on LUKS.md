```bash
sudo cryptsetup luksFormat /dev/disk/by-partlabel/t15_system && \
sudo cryptsetup luksOpen /dev/disk/by-partlabel/t15_system t15_system && \
sudo mkfs.btrfs -f /dev/mapper/t15_system
```

```bash
sudo mkdir -p /mnt/btrfs && \
sudo mount /dev/mapper/t15_system /mnt/btrfs && \
cd /mnt/btrfs
```

```bash
distname=ubuntu && \
sudo mv -vf @     @${distname} && \
sudo mv -vf @home @${distname}_home

sudo mkdir -p /mnt/${distname} && \
sudo mount /dev/mapper/t15_system -o subvol=@${distname}      /mnt/${distname}  && \
sudo mount /dev/mapper/t15_system -o subvol=@${distname}_home /mnt/${distname}/home && \
mp=/mnt/${distname}/boot     && sudo mkdir -p ${mp} && sudo mount /dev/disk/by-partlabel/t15_boot_${distname} ${mp} && \
mp=/mnt/${distname}/boot/efi && sudo mkdir -p ${mp} && sudo mount /dev/disk/by-partlabel/t15_efi ${mp}

apt install arch-install-scripts && arch-chroot /mnt/${distname}
```

```shell
echo 'CRYPTROOT=target=t15_system,source=/dev/disk/by-partlabel/t15_system' >> /etc/initramfs-tools/conf.d/cryptroot && \
echo 'export CRYPTSETUP=y' > /etc/initramfs-tools/conf.d/cryptsetup && \
echo "CRYPTSETUP=y" >> /usr/share/initramfs-tools/conf-hooks.d/cryptsetup && \
echo "CRYPTSETUP=y" >> /usr/share/initramfs-tools/conf-hooks.d/forcecryptsetup
```

```bash
nano /etc/default/grub
```

```nano
GRUB_CMDLINE_LINUX="cryptopts=target=t15_system,source=/dev/disk/by-partlabel/t15_system rootflags=subvol=@ubuntu"
```

```shell
sed -i "s|UUID=${SWAPUUID}|/dev/mapper/t15_swap|" /etc/fstab
sed -i "s|subvol=@|/subvol=@ubuntu|" /etc/fstab
cat /etc/fstab
```
```
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
/dev/mapper/t15_system 			/               btrfs   defaults,subvol=@ubuntu,ssd,noatime,space_cache,commit=120,compress=zstd	 	0
/dev/disk/by-partlabel/t15_boot_ubuntu	/boot           ext4    defaults       	 					0       2
/dev/disk/by-partlabel/t15_efi  		/boot/efi       vfat    umask=0077      				0       1
/dev/mapper/t15_system 			/home           btrfs   defaults,subvol=@ubuntu_home,ssd,noatime,space_cache,commit=120,compress=zstd 	0       0
/dev/mapper/t15_swap   			none            swap    sw              					0       0
```



```shell 
echo "t15_system /dev/disk/by-partlabel/t15_system none luks,discard" >> /etc/crypttab
echo "t15_swap /dev/disk/by-partlabel/t15_swap /dev/urandom swap,offset=1024,cipher=aes-xts-plain64,size=512" >> /etc/crypttab
cat /etc/crypttab
```
```
# cryptdata UUID=8e893c0f-4060-49e3-9d96-db6dce7466dc none luks
# cryptswap UUID=9cae34c0-3755-43b1-ac05-2173924fd433 /dev/urandom swap,offset=1024,cipher=aes-xts-plain64,size=512
```


```shell
update-initramfs -u -k all && \
update-grub && \
grub-install --target=x86_64-efi --efi-directory=/boot/efi /dev/nvme0n1
```

```shell
sudo apt-add-repository ppa:rodsmith/refind && \
sudo apt-get update && \
sudo apt-get install refind
```

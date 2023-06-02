---
template: overrides/main.html
title: Gentoo Linux Installation
---

# How to install Gentoo with EFI boot loader and OpenRC sysControl

## <center>[AMD64 Handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page)</center>

### Network
#### Determine interface names
```shell
root #ifconfig

eth0      Link encap:Ethernet  HWaddr 00:50:BA:8F:61:7A
          inet addr:192.168.0.2  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: fe80::50:ba8f:617a/10 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1498792 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1284980 errors:0 dropped:0 overruns:0 carrier:0
          collisions:1984 txqueuelen:100
          RX bytes:485691215 (463.1 Mb)  TX bytes:123951388 (118.2 Mb)
          Interrupt:11 Base address:0xe800 
```
#### Configure the proxies
```shell
#http_proxy
root #export http_proxy="http://proxy.gentoo.org:8080"
#ftp_proxy
root #export ftp_proxy="ftp://proxy.gentoo.org:8080"
#rsync_proxy
root #export RSYNC_PROXY="proxy.gentoo.org:8080"
```

#### Netowork automatic config Tool
```shell
root #net-setup eth0
```
#### DHCP
```shell
root #dhcpcd eth0
```
#### Preparing the wireless access
```shell
root #iw dev wlp9s0 info
Interface wlp9s0
  ifindex 3
  wdev 0x1
  addr 00:00:00:00:00:00
  type managed
  wiphy 0
  channel 11 (2462 MHz), width: 20 MHz (no HT), center1: 2462 MHz
  txpower 30.00 dBm
```
```shell
root #iw dev wlp9s0 link
Not connected.
```
```shell
root #ip link set dev wlp9s0 up
```
!!! note ""
    If the wireless network is set up with WPA or WPA2, then wpa_supplicant needs to be used. For more information on configuring wireless networking in Gentoo Linux, please read the Wireless networking chapter in the Gentoo Handbook.

### Prepare the disks
- GUID Partition Table - he GUID Partition Table (GPT) setup (also called GPT disklabel) uses 64-bit identifiers for the partitions. The location in which it stores the partition information is much bigger than the 512 bytes of the MBR partition table (DOS disklabel), which means there is practically no limit on the amount of partitions for a GPT disk. Also the size of a partition is bounded by a much greater limit (almost 8 ZiB - yes, zebibytes). 

#### Default partitioning scheme
| Partition   | Filesystem   | Size  |  Description   |
| ----------- | -------------|-------|----------------|
| `/dev/sda1`   | fat32 (UEFI) or ext4 (BIOS - aka Legacy boot)   | 256M  | Boot/EFI system partition|
| `/dev/sda2`   | swap   |  RAM size \*2    | Swap partition  |  
| `/dev/sda3`   | ext4   |  Rest of the disk    | Root partition  |

#### Filesystems
[**btrfs**](https://wiki.gentoo.org/wiki/Btrfs)
!!! note ""
    A next generation filesystem that provides many advanced features such as snapshotting, self-healing through checksums, transparent compression, subvolumes, and integrated RAID. Kernels prior to 5.4.y are not guaranteed to be safe to use with btrfs in production because fixes for serious issues are only present in the more recent releases of the LTS kernel branches. Filesystem corruption issues are common on older kernel branches, with anything older than 4.4.y being especially unsafe and prone to corruption. Corruption is more likely on older kernels (than 5.4.y) when compression is enabled. RAID 5/6 and quota groups unsafe on all versions of btrfs. Furthermore, btrfs can counter-intuitively fail filesystem operations with ENOSPC when df reports free space due to internal fragmentation (free space pinned by DATA + SYSTEM chunks, but needed in METADATA chunks). Additionally, a single 4K reference to a 128M extent inside btrfs can cause free space to be present, but unavailable for allocations. This can also cause btrfs to return ENOSPC when free space is reported by **df**. Installing [sys-fs/btrfsmaintenance](https://packages.gentoo.org/packages/sys-fs/btrfsmaintenance) and configuring the scripts to run periodically can help to reduce the possibility of ENOSPC issues by rebalancing btrfs, but it will not eliminate the risk of ENOSPC when free space is present. Some workloads will never hit ENOSPC while others will. If the risk of ENOSPC in production is unacceptable, you should use something else. If using btrfs, be certain to avoid configurations known to have issues. With the exception of ENOSPC, information on the issues present in btrfs in the latest kernel branches is available at the btrfs [status page](https://btrfs.readthedocs.io/en/latest/Status.html).

| Filesystem    | Creation command    | On minimal CD？    | Package   |
| --------      | ---------------     | ---------------    |  ------   |
| btrfs     | mkfs.btrfs    | Yes   | [sys-fs/btrfs-progs](https://packages.gentoo.org/packages/sys-fs/btrfs-progs)   |   
| ext4      | mkfs.ext4     | Yes   | [sys-fs/e2fsprogs](https://packages.gentoo.org/packages/sys-fs/e2fsprogs)     |

- EFI system partition partition (/dev/sda1) as FAT32 and the root partition (/dev/sda3) as ext4 as used in the example partition structure
```shell
root #mkfs.vfat -F 32 /dev/sda1
root #mkfs.ext4 /dev/sda3
root #mkswap /dev/sda2
```
- To activate the swap partition
```shell
root #swapon /dev/sda2
```

- Mounting the root partition
```shell
root #mkdir --parents /mnt/gentoo
root #mount /dev/sda3 /mnt/gentoo
```

### Installing stage3
#### Verify the current date and time by running the date command: 
```shell
root #date

Mon Oct  3 13:16:22 PDT 2021
```

Official Gentoo live environments include the chronyd command (available through the net-misc/chrony package) and a configuration file pointing to ntp.org time servers. It can be used to automatically sync the system clock to UTC time using a time server. Using this method requires a working network configuration and may not be available on all architectures. 
```shell
root #chronyd -q
```

#### Choosing a stage tarball
**Multilib (32 and 64-bit)**
!!! pied-piper "Multilib"
    Choosing a base tarball for the system can save a considerable amount of time later on in the installation process, specifically when it is time to choose a system profile. The selection of a stage tarball will directly impact future system configuration and can save a headache or two later on down the line. The multilib tarball uses 64-bit libraries when possible, and only falls back to the 32-bit versions when necessary for compatibility. This is an excellent option for the majority of installations because it provides a great amount of flexibility for customization in the future. Those who desire their systems to be capable of easily switching profiles should download the multilib tarball option for their respective processor architecture.

    Most users should not use the 'advanced' tarballs options; they are for specific software or hardware configurations.

**No-multilib(pure 64-bit)**
!!! pied-piper "No-Multilib"
    Selecting a no-multilib tarball to be the base of the system provides a complete 64-bit operating system environment. This effectively renders the ability to switch to multilib profiles improbable, although still technically possible.
??? warning
    Readers who are just starting out with Gentoo should not choose a no-multilib tarball unless it is absolutely necessary. Migrating from a no-multilib to a multilib system requires an extremely well-working knowledge of Gentoo and the lower-level toolchain (it may even cause our Toolchain developers to shudder a little). It is not for the faint of heart and is beyond the scope of this guide.

#### OpenRC
[OpenRC](https://wiki.gentoo.org/wiki/OpenRC) is a dependency-based init system (responsible for starting up system services once the kernel has booted) that maintains compatibility with the system provided init program, normally located in /sbin/init. It is Gentoo's native and original init system, but is also deployed by a few other Linux distributions and BSD systems.

OpenRC does not function as a replacement for the /sbin/init file by default and is 100% compatible with Gentoo init scripts. This means a solution can be found to run the dozens of daemons in the Gentoo ebuild repository. 

#### systemd
systemd is a modern SysV-style init and rc replacement for Linux systems. It is used as the primary init system by a majority of Linux distributions. systemd is fully supported in Gentoo and works for its intended purpose. If something seems lacking in the Handbook for a systemd install path, review the [systemd article](https://wiki.gentoo.org/wiki/Systemd) before asking for support. 

#### Donwloading the stage tarball
- Graphical browsers
```shell
root #cd /mnt/gentoo
root #wget <PASTED_STAGE_URL>
```

- Command-line browsers
```shell
root #links https://www.gentoo.org/downloads/mirrors/
root #links -http-proxy proxy.server.com:8080 https://www.gentoo.org/downloads/mirrors/
root #lynx https://www.gentoo.org/downloads/mirrors/
```
 - Verifying and validating
 ```shell
 root #openssl dgst -r -sha512 stage3-amd64-<release>-<init>.tar.xz
 root #sha512sum stage3-amd64-<release>-<init>.tar.xz
 root #openssl dgst -r -whirlpool stage3-amd64-<release>-<init>.tar.xz
 ```

#### Unpacking the stage tarball
```shell
root #tar xpvf stage3-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

#### <span class="jade">Configuring compile options</span>
To optimize the system, it is possible to set variables which impact the behavior of Portage, Gentoo's officially supported package manager. All those variables can be set as environment variables (using export) but setting via export is not permanent. 
```shell
root #nano -w /mnt/gentoo/etc/portage/make.conf
```

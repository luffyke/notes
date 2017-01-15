```
> df -hT
Filesystem     Type   Size  Used Avail Use% Mounted on
/dev/vda1      ext4    20G   12G  7.0G  63% /
tmpfs          tmpfs  7.8G     0  7.8G   0% /dev/shm
/dev/vdb1      ext4   197G  185G  2.3G  99% /data
```

```
mkdir -p /data/backup
```

```
> fdisk -l
Disk /dev/vda: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00078f9c

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *           1        2611    20970496   83  Linux

Disk /dev/vdb: 214.7 GB, 214748364800 bytes
16 heads, 63 sectors/track, 416101 cylinders
Units = cylinders of 1008 * 512 = 516096 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xc741327a

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1               1      416101   209714872+  83  Linux

Disk /dev/vdc: 214.7 GB, 214748364800 bytes
16 heads, 63 sectors/track, 416101 cylinders
Units = cylinders of 1008 * 512 = 516096 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```

```
> fdisk /dev/vdc
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x1668eeb3.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 1
First cylinder (1-416101, default 1):
Using default value 1
Last cylinder, +cylinders or +size{K,M,G} (1-416101, default 416101):
Using default value 416101

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

```
fdisk -l
```

```
> mkfs.ext4 /dev/vdc1
mke2fs 1.41.12 (17-May-2010)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
13107200 inodes, 52428718 blocks
2621435 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
1600 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
	4096000, 7962624, 11239424, 20480000, 23887872

Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 39 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override
```

```
echo /dev/vdc1 /data/backup ext4 defaults 0 0 >> /etc/fstab
cat /etc/fstab
mount /dev/vdc1 /data/backup/
```

```
> df -hT
Filesystem     Type   Size  Used Avail Use% Mounted on
/dev/vda1      ext4    20G   12G  7.0G  63% /
tmpfs          tmpfs  7.8G     0  7.8G   0% /dev/shm
/dev/vdb1      ext4   197G  185G  2.3G  99% /data
/dev/vdc1      ext4   197G  188M  187G   1% /data/backup
```

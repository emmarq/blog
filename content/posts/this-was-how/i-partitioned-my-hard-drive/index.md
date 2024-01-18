+++
title = 'This Was How I Partitioned My Hard Drive'
date = 2024-01-16T18:20:37-05:00
draft = false
+++

I am assuming you have a basic knowledge of what a shell, the command line and partitions are. If not, i strongly suggest you to watch or read some about this topics.

The final result of this write up is a drive with 2 partitions.

In a shell, I used `fdisk` to list the drives i had plugged in
```
sudo fdisk -l
```

Which yielded a lot of output. Within that output, i saw the info of the drive
```
Disk /dev/sda: 931.51 GiB, 1000204883968 bytes, 1953525164 sectors
Disk model: 035-1RK172      
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 8FAE9670-2F8E-458C-9308-AEE1DA2318E3
```

I knew this was the drive because it matched the storage capability. It was also noticed that the reference to the drive was `/dev/sda` in my case.

Knowing that reference, i could use fdisk to manage the partitions like so:
```
sudo fdisk /dev/sda
```
This command introduce us inside an "interactive prompt" where it can be given options for different operations. Each operation is represented with just a letter. I just used 'p' (to see partitions) and 'n' (to create partitions) and 'w' (to write changes). 
```
Welcome to fdisk (util-linux 2.38.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): 
```

First, i typed p, to see the partitions.
```
Command (m for help): p
Disk /dev/sda: 931.51 GiB, 1000204883968 bytes, 1953525164 sectors
Disk model: 035-1RK172      
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 8FAE9670-2F8E-458C-9308-AEE1DA2318E3
```
No partition info, just the device. Now I typed n, in order to create the partition. 
``` 
Command (m for help): n
Partition number (1-128, default 1): 1
First sector (34-1953525130, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-1953525130, default 1953523711): +460G

Created a new partition 1 of type 'Linux filesystem' and of size 460 GiB.
```
This command asked about the partition number, the first sector, and the size. In other words, it was asking a number to identify the partition, and from where, to where inside the disk was gonna be reserved for the data of the partition. (but you should already know something about it).

The changes were not applied yet, for that i would have to type 'w', but as i want another partition, i repeat the process of the 'n' command. After doing it, I could see the preview with the 'p' command:
```
Command (m for help): p
Disk /dev/sda: 931.51 GiB, 1000204883968 bytes, 1953525164 sectors
Disk model: 035-1RK172      
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 8FAE9670-2F8E-458C-9308-AEE1DA2318E3

Device         Start        End   Sectors  Size Type
/dev/sda1       2048  964691967 964689920  460G Linux filesystem
/dev/sda2  964691968 1950353407 985661440  470G Linux filesystem
```
The partitions are ready to settled. Now i can write the changes with 'w'.
```
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

Now is a good moment to create a file system in the partitions with the mkfs command

```
sudo mkfs.ext4 /dev/sda1
sudo mkfs.ext4 /dev/sda2
```

But i didn't because i had other plans. You can do it if that's all you wanted.


Awesome, i just partitioned my drive with 2 partitions. 
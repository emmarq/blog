+++
title = 'This Was How I Created a Degraded Raid 1 for My Only Drive'
date = 2024-01-16T18:26:23-05:00
draft = false
+++

Disclaimer: the moment of this write up, as most of mines, were the first one i do the thing. so take this with a grain of salt and don't be surprised if i do some hacky dirty stuff. And if i do,  please point people to the right direction. thank you.

I am assuming you know about what raid is (at least as little as me), partitions, the shell, and the command line. 

In short RAID is all about replication, fault tolerance and high availability of data in a computing system. For RAID 1, it is needed to have more than 2 drives, but i only had one. Anyway, i reasoned that as it is possible for a RAID 1 to still work after one disk may fail, there must be possible to start a RAID 1 and, later on, append the other drive. A quick search gave me hope that it was possible. 

So, let's begin.

It is needed a fresh drive, already partitioned, at least with one partition. It is critical that the partitions you intend to use for RAID can be replicated with the same capacity in the other drive. In a previous post I created 2 partitions, I will continue from that.

I installed the package mdadm. 
```
sudo apt install mdadm
```

Then i ran the following command
```
sudo mdadm --create /dev/md0 -l 1 -n 2 /dev/sda1 missing
```
I assume that if i had another partition in another drive at this moment, i could replace the 'missing' with that. But as i dont, i set the missing and the raid was born degraded :) .
The command produced the following output
```
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? 
Continue creating array? (y/n) y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```
It gives me a warning about some metadata been added at the beggining which made the raid not suitable as boot device. As i dont plan to use this drive nor any of its partitions as boot, i dont care about it and proceed. If you do, you should do your homework. Or wait until i want to do that too. :)

I wanted to create another raid with another partitions, so i repeated the process with updated references
```
sudo mdadm --create /dev/md1 -l 1 -n 2 /dev/sda2 missing
```

Finally i create the file system for /dev/md0 with mkfs
```
sudo mkfs.ext4 /dev/md0
```

It yielded the following
```
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 120553216 4k blocks and 30138368 inodes
Filesystem UUID: 0dd12ecb-c976-44cb-8c25-6fcf1e442346
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
	102400000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): 
done
Writing superblocks and filesystem accounting information: done     
```
I repeated the same command with /dev/md1

Now i have a degraded RAID 1 for each of the 2 partitions. 

Sources
- https://documentation.suse.com/sles/15-SP1/html/SLES-all/cha-raid-degraded.html
- https://www.thomas-krenn.com/en/wiki/Mdadm_recover_degraded_Array_procedure
- https://unix.stackexchange.com/questions/712386/raid-1-on-two-partitions-is-this-a-good-thing-to-do
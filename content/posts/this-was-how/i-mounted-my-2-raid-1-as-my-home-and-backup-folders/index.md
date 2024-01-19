+++
title = 'This Was How I Mounted My 2 Raid 1 as My Home and Backup Folders'
date = 2024-01-16T18:30:06-05:00
draft = false
+++

I had a device with 2 partitions already formatted with a file system, ext4 in an array (raid) and an empty /home and /backup folder.

To mount a partition in a folder, i used
```
sudo mount /dev/md0 /home
```
every read or write inside /home would happen in the partition /dev/md0. But it will unmount automatically on shutdown. You can unmount the partition mannually with umount
```
sudo umount /dev/md0
```

To persist the mount between shutdowns, i edited the fstab file. 
```
sudo vi /etc/fstab
```
And added the following lines at the end
```
/dev/md0        /home   ext4    defaults        0       0
/dev/md1        /backup   ext4    defaults        0       0
```
I saved the file and restart the system

Some time later, i experienced a power outage, and after reboot, the label for the disk had changed to  /dev/md126 /dev/md127. That caused start up errors. For that reason i changed 2 things in the fstab file. 

1. Use UUID instead of those labels. to know the uuid i use 
```
sudo blkid /dev/md127
sudo blkid /dev/md126
```
And later set that in fstab.

2. Add `nofail` the mounting options in fstab along with the defaults options (which include many defaults like rw, etc)

At the end, my new /etc/fstab lines looked like this
```
UUID=0dd12ecb-c976-44cb-8c25-6fcf1e442346	/home	ext4	defaults,nofail 0   0
UUID=903e564b-0749-4fbd-8f17-cee7446ef4bc	/backup	ext4	defaults,nofail 0   0
```

Sources
- https://www.howtogeek.com/442101/how-to-move-your-linux-home-directory-to-another-hard-drive/
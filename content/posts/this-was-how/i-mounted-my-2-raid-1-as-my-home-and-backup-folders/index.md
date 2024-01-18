+++
title = 'This Was How I Mounted My 2 Raid 1 as My Home and Backup Folders'
date = 2024-01-16T18:30:06-05:00
draft = true
+++

I am assuming you have a basic knowledge of the shell, the command line and how mounting works on linux (at least as little as me). If not, i strongly suggest you to watch or read some about this topics.

You need a device with partitions already formatted with a file system, preferably ext4, because that is what i am using. You better also backup your data if there is any in the location you intend to use as mount point. I expect an empty /home and /backup folder.

To mount a partition in a folder, you can use the command mount
```
sudo mount /dev/sda1 /thefolder
```
every read or write inside /thefolder will happen in the partition /dev/sda1. But it will unmount automatically on shutdown. You can unmount the partition mannually with umount
```
sudo umount /dev/sda1
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

Sources
- https://www.howtogeek.com/442101/how-to-move-your-linux-home-directory-to-another-hard-drive/
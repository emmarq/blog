+++
title = 'This Was How I Created Users and a Group in Linux'
date = 2024-01-16T18:33:10-05:00
draft = true
+++

Final result

The existence of 2 new users, both with a home directory for personal data, and some folders also managed by a special syncthing group.

To create the user i used the command `useradd`, like this: 
```
sudo useradd -m emmauel
```
The m flag instruct to create its respective home directory, by default `/home/emmanuel`. Next, I setted the password for the user with the command:
```
sudo passwd emmanuel
```
Wich asked about the new password for the user. After this, the emmanuel user can login in the system and read and write data in its home folder.

To create the other user, i repeated the same steps with another name.

Next thing was to create a new group:
```
sudo groupadd syncthing
```

And to add a user to the new group, i used the usermod command. In this case i added the user pi to the syncthing group too. The pi user is one that can execute commands as sudo, the only one in my case. I decided to use this user for service and admin task. 
```
sudo usermod -aG syncthing pi
``` 

Now i have new users, and a group `syncthing` with my `pi` user in it.
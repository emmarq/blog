+++
title = 'This Was How I Changed Borg to Restic'
date = 2024-01-16T18:54:50-05:00
draft = true
+++

After more carefull reading, i decided to ditch borg because what i intend to do was to copy the repo to the cloud, they suggest against. https://borgbackup.readthedocs.io/en/stable/faq.html#can-i-copy-or-synchronize-my-repo-to-another-location If i had a friend in my nebula network willing to give me space to store my backups, i would continue using borg, but i don't.

Instead i opt in with restic, that has explicit support of what i am intend to do. and apparently had a bigger community than borg according to the stars in its github repo. 

So, i installed restic
```
sudo apt-get install restic
```
Initiated the repo in the backup folder, because yes, i already had that special place
```
pi@raspberrypi:~ $ sudo restic init --repo /backup
enter password for new repository: 
enter password again: 
created restic repository f757041d67 at /backup

Please note that knowledge of your password is required to access
the repository. Losing your password means that your data is
irrecoverably lost.
```
And then edited the backup file i already had with borg to use restic instead. The following work is almost the same as i did previously with borg. The difference was that i added a remote backup with backblaze, and ditch the automatic prune. 
```
#!/bin/sh

# Setting this, so the repo does not need to be given on the commandline:
export RESTIC_REPOSITORY=/backup
export RESTIC_PASSWORD="'AsVdO{|Cy_Ej>S1a!e:_'fU'"

# some helpers and error handling:
info() { printf "\n%s %s\n\n" "$( date )" "$*" >&2; }
trap 'echo $( date ) Backup interrupted >&2; exit 2' INT TERM

info "Starting backup"

# Backup /home

restic --verbose backup /home

backup_exit=$?

info "Backing up remotely"

restic -r s3:s3.us-east-005.backblazeb2.com/bucket_name --verbose backup /home

remote_backup_exit=$?

# use highest exit code as global exit code
global_exit=$(( backup_exit > remote_backup_exit ? backup_exit : remote_backup_exit))

if [ ${global_exit} -eq 0 ]; then
    info "Backup and remote backup finished successfully"
elif [ ${global_exit} -eq 1 ]; then
    info "Backup and/or remote backup finished with warnings"
else
    info "Backup and/or remote backup finished with errors"
fi

exit ${global_exit}
```

For the remote backup I choose backblaze because many people in the backup sphere used it too. So i registered there, created a bucket with a unique name, and copy the s3 compatible url. 

Then defined 2 env vars as amazon s3 because at this time, restic suggest to use the s3 integration instead of the backblaze one. and initiated the repo. 
```
export AWS_ACCESS_KEY_ID=<MY_ACCESS_KEY>
export AWS_SECRET_ACCESS_KEY=<MY_SECRET_ACCESS_KEY>
restic -r s3:s3.us-east-005.backblazeb2.com/bucket_name init
```
The aws access key id is the backblaze appication key id, and the aws secret access key is the backblaze applicaction key secret. I created an application key just for the backup bucket created previously. 

I granted permition for execution to that script, and spint it up as simple as 
```
sudo chmod 755 backup.sh
sudo ./backup.sh
```
It took a while, but for my initial 13GB was remarkably fast, remote included. Meanwhile i edited the sudo crontab to execute the backup script every day at 1:30 am. 
```
sudo crontab -e
``` 
in the opened file i added the line: 
```
30 1 * * * /home/pi/backup.sh
```
Then it was a matter of waiting the next day to check if everything was ok. In that case, i have an automatic backup of home with snapshot and deduplicated.
 
Now that i think about it... i am missing the backup of the immich database... For another post it is. With the work done so far, a fresh immich instalation is still posible with the actual photos. already backed up. 
+++
title = 'This Was How I Set Up BORG'
date = 2024-01-16T18:52:46-05:00
draft = false
+++

Final result: A backup system with snapshot and deduplication in a dedicated partition for another partition

From previous post, i have a drive with 2 partitions, in one i mounted /home and in the other one i mounted /backup. Now is the moment that makes sense. 

The goal is to have a backup of home in the /backup folder, located in another partition. TBH that was self evident since the beginning.

Anyway, the solution i decided to go to is borg. https://borgbackup.readthedocs.io/en/stable/index.html 

Installing it was as simple as:
```
sudo apt install borgbackup
``` 

Later, creating the backup repo: 
```
sudo borg init --encryption=repokey /backup
```
That asked for a passphrase, a password. Also noticing, I used sudo because there can be a lot of users using files in home, and i dont want any problem accessing those. 

Borg tells to backup in a different place a passphrase and a repo key. I am gonna do that. To export the repo key, i executed: 
```
sudo borg key export /backup
```
I saved the output and the passphrase in my password manager, pass. That is for another post. 

Then it was time to actually made the backup. For that i decided to use the automatic backup script directly, to take advantage of the standar name of the backup. I saved the file in `/home/pi`and it was like this:
```
#!/bin/sh

# Setting this, so the repo does not need to be given on the commandline:
export BORG_REPO=/backup

# See the section "Passphrase notes" for more infos.
export BORG_PASSPHRASE="XYZl0ngandsecurepa_55_phrasea&&123"

# some helpers and error handling:
info() { printf "\n%s %s\n\n" "$( date )" "$*" >&2; }
trap 'echo $( date ) Backup interrupted >&2; exit 2' INT TERM

info "Starting backup"

# Backup the most important directories into an archive named after
# the machine this script is currently running on:

borg create                         \
    --verbose                       \
    --filter AME                    \
    --list                          \
    --stats                         \
    --show-rc                       \
    --compression lz4               \
    --exclude-caches                \
    --exclude 'home/*/.cache/*'     \
                                    \
    ::'{hostname}-{now}'            \
    /home                          

backup_exit=$?

info "Pruning repository"

# Use the `prune` subcommand to maintain 7 daily, 4 weekly and 6 monthly
# archives of THIS machine. The '{hostname}-*' matching is very important to
# limit prune's operation to this machine's archives and not apply to
# other machines' archives also:

borg prune                          \
    --list                          \
    --glob-archives '{hostname}-*'  \
    --show-rc                       \
    --keep-daily    7               \
    --keep-weekly   4               \
    --keep-monthly  6

prune_exit=$?

# actually free repo disk space by compacting segments

info "Compacting repository"

borg compact

compact_exit=$?

# use highest exit code as global exit code
global_exit=$(( backup_exit > prune_exit ? backup_exit : prune_exit ))
global_exit=$(( compact_exit > global_exit ? compact_exit : global_exit ))

if [ ${global_exit} -eq 0 ]; then
    info "Backup, Prune, and Compact finished successfully"
elif [ ${global_exit} -eq 1 ]; then
    info "Backup, Prune, and/or Compact finished with warnings"
else
    info "Backup, Prune, and/or Compact finished with errors"
fi

exit ${global_exit}
```
I granted permition for execution to that script, and spint it up as simple as 
```
sudo chmod 755 backup.sh
sudo ./backup.sh
```
It took a while. Meanwhile i edited the sudo crontab to execute the backup script every day at 1:30 am. 
```
sudo crontab -e
``` 
in the opened file i added the line: 
```
30 1 * * * /home/pi/backup.sh
```
Then it was a matter of waiting the next day to check if everything was ok. In that case, i have an automatic backup of home in snapshot and deduplicated with the last 7 days, 4 weeks, and 6 months. 17 snapshot in total.
 
Now that i think about it... i am missing the backup of the immich database... For another post it is. With the work done so far, a fresh immich instalation is still posible with the actual photos. already backed up. Just one thing missing, the 1 in the 3,2,1 backup strategy, we are going cloud!
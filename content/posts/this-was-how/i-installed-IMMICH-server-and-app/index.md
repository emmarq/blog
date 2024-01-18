+++
title = 'This Was How I Installed IMMICH Server and App'
date = 2024-01-16T18:50:59-05:00
draft = true
+++

The final result is a replacement for, as immich said: App-Which-Must-Not-Be-Named. A cloud based photo gallery with user capabilities with syncronization by syncthing. 

To install IMMICH i installed 2 things, docker and IMMICH itself. And for both i followed to the letter  their respective how to. I think i dont have to add much to it. Except for some minor tweaks i did at the end for my special case.

For docker, i followed this instructions as i have an ARM64: https://docs.docker.com/engine/install/debian/#install-using-the-repository

And for IMMICH, i follow the instructions with the user pi in the home directory: https://immich.app/docs/install/docker-compose

They remind you to change some env vars if you want to use another password, or name for the database for example. I did'nt tho. I dont plan to expose that database outside so i don't see most trouble of leaving that as it is. for now. if i need to change it, i suspect that is possible getting into the container to access the database change the credentials, and then update the compose file. but that is a problem for another day, if it comes to that.

What i did change, was adding another path to the volume in two services (
https://immich.app/docs/features/libraries/#usage). Editing the file `/home/pi/immich-app/docker-compose.yml` to add  the line below the volumes of the immich-server and the immich-microservices: 
```
  immich-server:
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
      -  /etc/localtime:/etc/localtime:ro
+    - /home/emmanuel/fotos:/mnt/media/emmanuel:ro


  immich-microservices:
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
+    - /home/emmanuel/fotos:/mnt/media/emmanuel:ro
```

At this point point you made wonder, "why did you do this?" And you are right to cuestion it. I think i didn't trust the upload process of immich, in part was because the warning they give about not using immich as my solely way to backup, and that it was experimental, so i rely on syncthing for that. immich say it doesn't change external libraries but it treat them as normal as the upload library. That gives me some peace of mind to use immich as a front app for my photos, syncthing for the replication, and later on, another technology for the backup part. But after doing the whole thing, i am cuestioning my decisions, i now know that maybe they were just talking about backup, that doesn't have anything to do with upload. And   what i have used of immich so far is incredibly well done! so i am pondering to just let immich handles the upload process too. That idea is cooking. maybe i will, mabe i wont.

Anyway, it was time to start immich! as easy as running the following inside `/home/pi/immich-app`
```
docker compose up -d
```
Again, i let the instructions of the first steps with immich to them: https://immich.app/docs/install/post-install/

The admin user i created was myself. this part is important because the admin can create new users, and assign to them external path, i.e, places they can access in the volumes mounted for scan of photos. So, in the immich page http://raspberrypi.local:2283, i went to Administration -> Users, press the edit button of my user, setted the external path to `/mnt/media`, and confirm. 

Next i press my avatar -> Account settings -> Libraries -> Create external library. A new library appear. Press the button to edit it -> Edit import path -> Add path. In the dialog opened i did set the path to `/mnt/media/emmanuel` and save. 

We are almost there. This libraries update automatically each day at midnight. But an update can be forced with the scan options. and if some assets were deleted, because you deleted it in your phone and syncthing replicated the change, you can remove offline files. More and better documentation about it can be found here https://immich.app/docs/features/libraries#external-libraries. After doing all of that, photos were starting to appear in the timeline! 

Speaking of the phone, a nice immich app is available in the playstore, so i installed it. https://play.google.com/store/apps/details?id=app.alextran.immich. 

When opening the app it will ask about the server address. As i have nebula already configured, i added the port 2283 to my raspberry's role, and  voila, i could access to it's nebula assigned ip and connect to the immich server. My case is `http://100.100.0.6:2283/api`. Then it asked for the user. And now we are done. I can access to my photo library in my phone with immich. 

Another stuff i did, was disable machine learning. that is because i only have an rpi, i have mercy of it. It is possible to delegate that work to another machine, but that's something i haven't done so far. Even so, the brief moment it was on, it detected various faces and indexed the photos. Impresive. 
 
Now i have a really beautiful front end to explore my synced photos, where i can create albums, and navigate with faces, and time. 
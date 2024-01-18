+++
title = 'This Was How I Installed Nebula'
date = 2024-01-16T18:40:13-05:00
draft = true
+++

The final result is a mesh vpn where my devices can connect privately, p2p, through the internet as it was a lan.

The first time i installed nebula i managed the certificates, the nodes, the ip range, etc... It wasn't that difficult, all is well documented here: https://nebula.defined.net/docs/guides/quick-start/

But at the moment of this write up, i decided to go with another way, using the Managed Nebula Networks of Defined networking. https://www.defined.net/ 

They claim to offer a managed experience of the networking up to 100 host free, that is to my understanding, the creation, and exchange of certificates, and the addition of host, lighhouses, roles, firewall configuration, etc... 

So i registered there, but still i needed a machine exposed to the internet to act as a lighthouse (a lighthouse is a host for discoverability only, the connection between pairs after that occurred directly as a mesh network, i had monitored the network use of  the lighthouse machine and is very low considering a heavy use of the nebula network). This time i decided to go with the free tier of the gcp vm, if something happens, like unexpected billing, i still burning the free credits they give, and the consumption is very low, so i have time to evaluate another option for the lighthouse provider. 

Long history short, i created an e2-micro with exposed 4242 port in gcp (How i did that is way beyond the scope of this post). Later on, throug the web panel of defined networking, i added that machine as a lighthouse following the instructions they give inmediately. It goes like:

For lighthouses:
1. set the name
2. set the ip
3. set the role
4. save
5. follow the presented instructions in the machine
6. download dnclient
7. install dnclient
8. start dnclient
9. connect the dnclient with the token 
10. done

For host are the same, but an ip is not needed, and the presented instructions are meant to be run in the host machine. In my case, my raspberry pi, and my android phone. 

Once those steps are performed, I could ping my host with each other with a remarkable speed. 

One more thing to do, tho. My raspberry so far had services running in ports. if i wanted to expose those services to my nebula network, i had to edit the role given to my raspberry pi, and add the ports i want to exposed. So, in defined networking, obviously logged in, i go to Roles -> select the role i want to edit -> Add firewall rule 

There i set the protocol, the port, and the role a host must have to access the port. I did it with protocol tcp, port 8483, and role user.  After saving, he changes take effect almost inmediately. That way, hosts with the role user now can access to the syncthing service of the raspberry.

Any new service i add to my raspberry that i want to expose in the network, i have to repeat the process. 


https://cloud.google.com/free/docs/free-cloud-features?hl=es#compute
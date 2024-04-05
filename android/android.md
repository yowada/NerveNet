### Nervenet android application
In order to run the android application, we need to install the boat sqlite docker image on the lanner devices.<br>
We also need to install 4 android application on several android devices.<br>
Installing boat sqlite docker container

```
docker load < nervenet-boat-sqlite-1.2.8.docker.tar.gz
```
```
docker run --name=nervenet-boat -d --network=host --restart always nervenet-boat-sqlite:1.2.8
```

Edit the node.conf file inside the boat sqlite container<br>
To access the shell inside the conatiner
```
docker exec -it nervenet-boat /bin/sh
```
To edit the file
```
vi /writable/personal/node.conf
```
The content of the file should be
```
[NODE]
NODEID=200
NODENAME=sip_uri_for_this_node.local
BOATID=0x0101024247881bdf
```
The docs says to change the NODEID and NODENAME to unique value to use database synchronization features.

> The files corelate to DBSync_boxshare

### Android

The apps for nervenet android are:

1. NerveNet Application
2. Fairway Pilot
3. Nervenet-boat
4. Phone Depend

We can call from one phone to another using nervenet application and phone depend if they are connected to the nervenet mesh network

#### Using phone depend app

Using the phone depend app we can call other phones in the same network

1. Open phone depend app while connected to the nervenet mesh network (Connect a Access Point to 4th Port of the lanner device)
2. In the phone depend app, any other devices connected to the network will appear when pressed in ***Boat List***
3. Then after we have selected a boat list, we can then call the phones in the nervenet mesh.

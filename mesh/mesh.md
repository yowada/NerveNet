## Introduction
This document follows basic mesh ping testing as mentioned in file [Basic Reference test](assets/documents/NerveNet-SDN_BasicTest_Reference_Manual.docx)  

## Prerequisites:

Currently, we have nervenet running on 4 nodes. Each node is running debian server and is running the following docker containers: 
| Container Name | version |
| -------------- | ------- |
| nervenet-configurator-daemon | 1.0.0 |
| nervenet-configurator-http | 1.0.0 |
| nervenet-tsg-sqlite | 1.6.2 |

One of the node is running an additional container nervenet-ptmgr-node10 v1.1.4. The node running ptmgr container is referred to as Routing Tree Distribution Bureau. I have connected my device to the ptmgr node.

In order to update the version of nervenet-tsg-sqlite, please [click this link](https://github.com/sdhrt/nervenet-docs/blob/master/mesh/update_tsg.md)

#### Points to be noted

- The ports used for creating mesh on the lanner nca 1513b are port numbers 1,2 and 5.
- As the image shows in the reference test manual, we are setting up 4 base stations. However, we are only connecting one laptop to the ptmgr node. We are then going to ping the ip addresses assigned by the ptmgr node from one basestation to another.
- The IP that we are going to ping is the virtual adapter brseat.
- ***brseat*** is the bridge interface for the user access broadcast domain. Local DHCP server is connected to this bridge interface.
- ***brmesh*** is the bridge interface for the control communication between base stations. L2 control message between base stations are transmitted through this bridge interface.
- brseat and brmesh are not connected. So the same IP address is assigned for these bridge interfaces to be able to access using the IP address from the terminal side, and also from the other base stations.
- The brseat ip is assigned according to the base station id that is configured in the admin page. e.g. 172.16.x.1 where the x is replaced by the base-station ID. If we login to the admin console for the first time ([172.16.1.1/bsconfig/login](172.16.1.1/bsconfig/login) by default), we are prompted to enter the basestation ID and other details. Here we can assign an ID to the base-station in the range of 1-255.

### Ping setup

1. BS1 is running the ptmgr container.
2. BS1 is connected to the internet. We are using the 3rd port (enps90f0) to connect to the router
3. Currently, the link type for link port 1,2 and 3 is "None"
4. After connecting the nodes with each other in a mesh setup, we can observer that the status of Link port 1, 2 and 3 are UP.
5. Status of WAN is UP.
![admin console](/mesh/assets/images/admin_console.jpg)

### Ping testing

1. The document doesn't explicitly mention which ports are to be used when creating the mesh. But since the admin console says the Link port 1, 2, 3 and 4 uses ports enp3s0, enp2s0, enp10s0f0 and enp10s0f1 respectively. The table below shows which ports we are using in this test.

    | Port number | Connecction |
    | -------------- | ------- |
    | 1 | Mesh |
    | 2 | Mesh |
    | 3 | Ethernet |
    | 4 | Laptop |
    | 5 | Mesh |
    | 6 | Mesh |

2. In order to configure which port to use as the interface connection for network / internet, we can edit the file in the directory /etc/network/interfaces. We are going to use vi to edit this file.

    ```
    vi /etc/network/interfaces
    ```
    
    The current configuration uses 2nd port(enp2s0) as the network interface. In order to use the 3rd port(enp9s0f0), change every occurence of enp2s0 to enp9s0f0  

    > In vi you can use the following command :%s/enp2so/enp9s0f0/g  

    We are then going to restart the network in the device using the following command

    ```
    sudo systemctl restart networking
    ```
3. The BS1, BS2, BS3 and BS4 have base-station ID 1, 2, 3 and 4 respectively and the brseat IPs' are 172.16.1.1, 172.16.2.1, 172.16.3.1, 172.16.4.1 respectively.
4. Now we are going to ping from the laptop connected to BS1 to BS4. The IP of BS4 is 172.16.4.1
5. We are going to ping using the command in order to confirm the link between BS1 and other base stations.
```
ping 172.16.4.1
```
![ping BS4](/mesh/assets/images/BS4_ping.jpg)<br>
5. As the document says, now we are going to ping continuosly but remove the links between the nodes.(ping with t flag for conitinuos ping in cmd)  
```cmd
ping -t 172.16.4.1
```
  
![ping status after disconnection](/mesh/assets/images/BS4_ping_dis.jpg)<br>
6. As we can see, the direct connection between two nodes were disconnected after 4 ping packets, which then resulted in "request timed out" which was soon resolved using another path. After we lost the connection, the path was automatically resolved by Nervenet. This concludes the ping testing for the first scenario.

### Ping testing: ERB-UDP

1. Following the instructions on ping testing and changing the link type to ERB-UDP.
2. Edit /writable/personal/erb-linkd.conf to insert the following configuration.
```
[PROGRAM/LINKERB/(number of the port)]
USE_ERBUDP_SMART=yes
```
This configuration works on nervenet-tsg-sqlite.1.6.2 or higher.

3. The current state of admin console is:
![ping in LINK type ERB-UDP](/mesh/assets/images/erb_udp.jpg)<br>
4. We can confirm that link is up after migrating from "none" to erb_udp
5. After this, proceed with the ping test as mentioned [above](#ping-testing).

### ERB-TCP Configuration

#### *Assumptions:* 
ERB-TCP operates with two distinct roles: Server and Client. For the Server role to function correctly, it's essential to assign a static IP address, and the server node must make sure that port number 609 is reachable. If your network configuration includes a NAT (Network Address Translation) router, you'll need to configure port forwarding settings on the NAT router. This involves directing incoming packets with destination port number 609 to the ERB-TCP server base station.
> Make sure port 609 is forwarded to the server in erb-tcp <br> In this example, we are going to use port number 50.
1. Edit and insert the following lines into the ERB-TCP ***server*** side configuration files.

- /writable/personal/node.conf
```
[NODE/LINK/50]
DEV=erb50
COST=3
```

- /writable/personal/netdev/erb50
```
ETHER=erb50
```

- /writable/personal/erb-linkd.conf 
```
[PROGRAM/LINKERB/50]
SESSION=ANY-SESSION-PHRASES
USE_CIPHER=no
```
2. Edit and insert the following lines into the ERB-TCP ***client*** side configuration files.
- /writable/personal/node.conf
```
[NODE/LINK/50]
DEV=erb50
COST=3
```

- /writable/personal/netdev/erb50  (create if not exist)
```
ETHER=erb50
```

- /writable/personal/erb-linkd.conf (assuming server global IP address is 10.0.0.3)
```
[PROGRAM/LINKERB/50]
#SERVER_NAME=url.of_your.erb_tcp_server.com
#USE_DNS=yes
SERVER_ADDR=10.0.0.3
SESSION=ANY-SESSION-PHRASES
USE_CIPHER=no
```
3. Restart nervenet-tsg-sqlite docker container of both server and client.
4. Check erb-linkd.log and whisperd.log files to the link between these to nodes has been established.
5. After this, proceed with the ping test as mentioned [above](#ping-testing).

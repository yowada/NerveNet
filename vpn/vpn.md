We plan to establish a connection between our local Nervenet mesh network and the NICT Nervenet mesh network in Japan via VPN. To achieve this, we've conducted tests using ERB-TCP. As outlined in the [mesh.md](https://github.com/sdhrt/nervenet-docs/blob/master/mesh/mesh.md#erb-tcp-configuration) documentation, ERB-TCP operates with two distinct roles: client and server. Once we've configured the client and server roles correctly, we should able to successfully establish and utilize the VPN connection for communication between the two mesh networks.
The Nervenet mesh at Japan assumed the role of server whereas ICT4D assumed the role of client for this test. We also confirmed by reversing the roles.

### Example of how we setup VPN

1. First we need to have unique Basestation ID's.
2. We changed out IDs from 1,2,3 and 4 to 201, 202, 203 and 204.(at ICT4D)
3. The ISP router was connected to BS201 in port 3.
4. We then stopped ptmgr container in this node.
5. As mentioned in [mesh.md](https://github.com/sdhrt/nervenet-docs/blob/master/mesh/mesh.md#erb-tcp-configuration), we first changed the role of BS201 to assume the role of client.
6. Restart nervenet-tsg-sqlite docker container.
7. The Basestation at the NICT then assumed the role of the server.
8. After everything was setup, we could connect from ICT4D, Nepal to NICT, Japan.
9. We could ssh into NICT and vice versa using brseat ip address.
10. WIth this, the vpn test was concluded.

> The log files to be referred are /var/log/whsiperd.conf , /var/log/erb-linkd.log

### Findings

- The default gateway route for the basestation were missing so we had to do
    ```
    sudo ip route add default via 172.16.<BS_ID>.1
    ```
    This issue can be permanently resolved by uncommenting the line TUNROUTE=0.0.0.0/0 in the file /writable/personal/mpath.conf in the basestation connected to the internet

- During testing, VPN connection could not be established between Basestation 202 (in ICT4D, Nepal) and Basestation 1 (in NICT, Japan).  
The issue stemmed from a prior configuration on Basestation 202, which mistakenly assigned it the role of a client, causing it to connect to Basestation 201 in ICT4D, Nepal. This configuration prevented Basestation 1 from establishing a connection with Basestation 202. The problem was resolved by removing out the configuration that was causing Basestation 202 to function as a client and connect to Basestation 201.

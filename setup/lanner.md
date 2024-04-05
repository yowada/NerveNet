# Setting up nervenet

### Contents

- [Installing debian server on the lanner device](#installing-debian-server-on-the-lanner-device)  
- [Installing tools and docker in the lanner device](#setting-up-tool-and-docker)  
- [Installing Nervenet](#installing-nervenet)  

## Installing debian server on the lanner device

1. We need to flash a Debian server into a flash drive (USB) which can be downloaded by clicking on [this link](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.7.0-amd64-netinst.iso).
2. Use a software such as [Balena Etcher](https://balena.io/etcher) in order to flash the image downloaded earlier into the usb.
3. Connect a usb-serial(console) cable to the Lanner device from your laptop.
4. Attach the usb drive to the lanner device.
5. Use 2nd port in the lanner device to connect to the router(with internet access) using an ethernet cable.
6. We will use [putty](https://putty.org) in order to connect to the device through serial connection.
7. After the console cable has been attached, open Device Manager -> Ports and note down the communication port of prolific USB-to-Serial Com Port e.g. COM3
8. Double click on the port to open device properties and change port settings. Change the following settings:
    - bits(baud) per second to 115200 
    - Data bits to 8 
    - Parity to none. 
    - Stop bits to 1. 
    - Flow control to none. 
9. Open putty. Set connection type to Serial and set speed to 115200. Configure Serial connection settings to the same in Step 8 or replicate the settings listed in the images below.

![putty](/setup/assets/images/putty1.jpg)
![putty2](/setup/assets/images/putty2.jpg)

10. Connect the power cable to the lanner device (it will usually turn on by default) and turn it on.
11. Press TAB or DEL to enter the BIOS if you need to set boot priority.
12. Select Install and press TAB (Do not press Enter).
13. Change from  
    ```
    vga=768
    ```
    ```
    vga=off console=ttyS0,115200n8
    ```

    and leave the other configuration as it is

    ![debian install configuration](/setup/assets/images/debian_install.jpg "debian")

14. Press Enter
15. Select English language

![lang](/setup/assets/images/language.jpg)

16. Region, select Other > Nepal.
17. Set locale as en_US.UTF-8

![locale](/setup/assets/images/locale.jpg)

18. The next step is to configure the network interface in the device. In this setup, you are to choose the port which the network is connected to. If you are using the 2nd port, you should choose enp2s0 and it will start to configure dhcp automatically.

![network](/setup/assets/images/network.jpg)

19. Select a suitable hostname

![hostname](/setup/assets/images/hostname.jpg)

20. Set root password

![rootpass](/setup/assets/images/rootpass.jpg)

21. Enter full name.
22. Enter username for a user.

![username](/setup/assets/images/username.jpg)

23. Enter password for the user

![userpass](/setup/assets/images/userpass.jpg)

24. Reenter the password to verify.
25. Partitioning method, select guided - use entire disk

![partition](/setup/assets/images/partition.jpg)

26. Select ssd as the image below.

![partition2](/setup/assets/images/partition2.jpg)

27. Select all files in one partition for the partitioning scheme.

![partition3](/setup/assets/images/partition3.jpg)

28. Select finish.

![partition4](/setup/assets/images/partition4.jpg)

29. Confirm changes.

![partition5](/setup/assets/images/partition5.jpg)

30. Select Mirror as India

![mirror](/setup/assets/images/mirror.jpg)

31. Select archive mirror url as the default value.

![packagemirror](/setup/assets/images/packagemirror.jpg)

32. Leave proxy blank

![proxy](/setup/assets/images/proxy.jpg)

33. Opt out of popularity contest by selecting no.

![popularity](/setup/assets/images/popularity.jpg)

34. Only select ssh and standard system utilities for a minimal installation and wait for it to install.

![softwareselection](/setup/assets/images/softwareselection.jpg)

35. Select ssd to install GRUB bootloader.

![grub](/setup/assets/images/grub.jpg)

36. Select Install and after it reboots, you can eject the USB drive with the debian server image.

![installfinal](/setup/assets/images/installfinal.jpg)

37. Now wait for grub bootloader to appear(there is a timeout and it will automatically proceed if nothing is pressed) and press **e** in order to edit the boot settings.

![gnugrub](/setup/assets/images/gnugrub.jpg)
![grubconfig](/setup/assets/images/grubconfig.jpg)

38. Add the following parameter as shown in the image.

```
console=ttyS0,115200n8
```

![grubcmd](/setup/assets/images/grubconfig2.jpg)

39. press F10
40. Now you are asked to login to the debian server, Enter root as username and password for root set in **step 20**

41. Now we should edit the /etc/default/grub file to change some settings for boot. Use nano or vi to edit the file by
    ```bash
    vi /etc/default/grub
    ```
42. Change the value of GRUB_CMDLINE_LINUX to "console=ttyS0,115200n8" as shown in the image below

![grubcmd](/setup/assets/images/grubcmd.jpg)

43. Run the following command
    ```bash
    update-grub
    ```

44. Debian has been installed on the lanner nca-1513b

***

## Setting up tool and docker

Now we are going to install some basic packages for the debian server and docker in order to operate nervenet on the lanner device.

***

1. You should be in root for the following operation. You can switch to root by entering
    ```bash
    su -
    ```
    and entering root password

2. Install sudo

    ```
    apt install sudo
    ```

3. Add the user to the sudoers group by

    ```
    /usr/sbin/adduser <username> sudo
    ```

    ```
    /usr/sbin/usermod -aG sudo <username>
    ```

4. Login with the user account

    ```
    su <user>
    ```

    and enter the user password

5. Check if user can use sudo by running the command
    ```
    sudo echo hi
    ```

    Enter the user password.  
    If hi appears on the screen, you are good to go.

    ![runsudo](/setup/assets/images/runsudo.jpg)

6. Now we move on to setting up docker 
    
    1. Update existing packages
        ```
        sudo apt update
        ```

    2. Install dependencies
        ```
        sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg2 nginx php python3 bridge-utils net-tools 
        ```

    3. Install docker by running

        ```
        curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/debian.gpg
        ```

        Output: ok

    4. Run
        ```
        sudo add-apt-repository "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
        ```

    5. Check debian version
        ```
        lsb_release -cs
        ```
        output: bullseye(debian 11)
        if output is buster it indicates you have installed debian 10

    6. run 
        ```
        apt update
        ```
        ```
        apt -y install docker-ce docker-ce-cli containerd.io
        ```

    7. change current group id to docker
        ```
        # replace user1 with current user name
        sudo usermod -aG docker user1
        ```
        ```
        newgrp docker
        ```

7. Check if docker is installed by running
    ```
    docker --version
    ```

    ![dockerversion](/setup/assets/images/dockerversion.jpg)

***

## Installing nervenet

In order to install nervenet, we need to extract some files listed below and load the required files into docker and run it. The required files are:

- linux-nsgs-installer.tar.gz (gunzipped folder where the installer resides)
- nca-1513b.patch (old)
- lanner.patch (new, replaces nca-1513b.patch)

- nervenet-tsg-sqlite.docker.tar.gz (docker container)
- nervenet-configurator-daemon.docker.tar.gz (docker container)
- nervenet-configurator-http.docker.tar.gz (docker container)

- nervenet-ptmgr-node10 (This file is only required for the route tree distribution bureau / Base Station)

***

1. We first need to tranfer required files in to the lanner box.
2. You can either use a usb drive or use sftp server to transfer the files.
3. Transfering the required files using sftp
    1. Determine the ip address of the lanner device on the local device.
    2. Run
        ```
        ip a
        ```

        ![ipa](/setup/assets/images/ipa.jpg)

    3. We can see that the network adapter enp2s0 (*determined by which port you are using to connect to the router*) is using address **192.168.1.77**
    4. Connect to the server using a client such as [filezilla](https://filezilla-project.org/download.php?type=client)
    5. Open filezilla

        ![filezilla](/setup/assets/images/filezilla.jpg)

    6. Login using
        
        - sftp://<ip address> e.g. ftp://192.168.1.77
        - <username> e.g. user1
        - <password> e.g. user1

    7. transfer files to /home/<user>/nervenet/ folder (free to choose name)
    8. transfer files from /home/<user>/nervenet to /usr/nervenet (create folder as root) and ensure ownership (chmod at least 744)

4. Using usb drive to transfer files
    
    1. Transfer files from usb while checking the mount and usb format.

5. cd into /usr/nervenet as root
6. Unzip the file linux-nsgs-nervenet-installer.tar.gz

    ```
    gunzip linux-nsgs-nervenet-installer.tar.gz
    ```

7. extract the files;

    ```
    tar -xvf linux-nsgs-nervenet-installer.tar
    ```

    a folder named "nervenet-installer" should appear

8. The patch file should reside in the same directory as the nervenet-installer folder
    e.g. ls should give:

    ```
    . .. nca-1513b.patch nervenet-installer
    ```

9. Run (use lanner.patch if available)

    ```
    patch -p 1 -d nervenet-installer < nca-1513b.patch
    ```

10. Run the following command inside the nervenet-installer folder

    ```
    sudo bash install.sh -m nca-1513b -t full -d sqlite
    ```

    ![Logs](/setup/assets/images/nervenetinstall.jpg)

11. Run:

    ```
    docker ps -a
    ```

    to check the running containers and if containers appear, then nervenet has been installed

12. Now we can install the nervenet-ptmgr-node10 docker container which is installed only in the base station.
13. First we laod the docker image by running the following command:

    ```
    docker load < nervenet-ptmgr-node10-1.1.4.docker.tar.gz
    ```

    ![docker ptmgr](/setup/assets/images/ptmgrload.jpg)

14. In order to run the container run the command
    
    ```
    docker run --name=nervenet-ptmgr -d --network=host --mount type=bind,src=/writable/personal,dst=/writable/personal --mount type=bind,src=/var/log,dst=/var/log --restart always nervenet-ptmgr-node10:1.1.4
    ```

    ![ptmgrrun.jpg](/setup/assets/images/ptmgrrun.jpg)


15. Now once the docker container is running succesfully, we should be able to access the admin page by accessing the base station's ip address.  
Enter ***ip address***/bsconfig/en e.g. 192.168.1.77/bsconfig/en  

![website](/setup/assets/images/adminpage.jpg)

16. If the website is not online, we may need to configure the device to operate nginx server rather than the apache2 http server.  
The port 80 may be currently occupied by the apache server. Confirm it by entering ***ip address*** e.g. 192.168.1.77 into the browser.  
If the default apache server pops up, we need to disable it.

17. Run the following commands as **root** in order to disable the apache server
    
    1.
    ```
    systemctl disable apache2
    ```

    2.
    ```
    systemctl status apache2
    ```

    ![apache2](/setup/assets/images/stopapache2.jpg)

    3.
    ```
    systemctl restart nginx
    ```

    4.  
    ```
    systemctl status nginx
    ```

    ![nginx](/setup/assets/images/statusnginx.jpg)

18. Now nginx should be running and the admin page (***ip address***/bsconfig/en) should be online

    ![adminpage](/setup/assets/images/adminpage.jpg)

19. The login credentials are:  
    username: admin  
    password: password  
    (You may need to input basestation id and device name after login)

20. You have now accessed the admin page

# Lab1

## Network topology
```
---------------------
|      Jumphost     |
|   10.10.2.254/24  |-----------------
| dnsmasq + mariadb |                 \
---------------------                  \
         |           \                  \
------------------  ------------------  ------------------
|      RH-1      |  |      RH-1      |  |      RH-1      |
| 10.10.2.101/24 |  | 10.10.2.101/24 |  | 10.10.2.101/24 |
|     Rancher    |  |     Rancher    |  |     Rancher    |
------------------  ------------------  ------------------
```

## Info
This lab is planned to run on VirtualBox v5. The underlaying machine must have at least **2CPU** and **4Go Ram**.

Each VM will run RancherOS v1.0.1 (you can download the iso on [github.com/rancher/os](https://github.com/rancher/os))

In the course material, you'll find the private key (`rancher-primer-lab.pem`) to access to VMs without password once they will be set up. [Download](https://github.com/de13/rancher-primer-lab/blob/master/keys/rancher-primer-lab.pem) the file in `~/.ssh/`.

The course material also provides you with a `config` file to falicitate access to VMs over `ssh`. Copy this file to `~/.ssh/config` on your computer, and **set perms to 0600** on the file. [Download](https://github.com/de13/rancher-primer-lab/blob/master/config) the file.

## Steps to create VMs

**1. Create a NAT Network in VirtualBox**

Depending on your OS, in general settings, then Networks.
![NAT Network](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig01.png)
Create a network called "rancher-lab-primer" or something else, the name doesn't matter. More important is the **CDIR: 10.10.2.0/24**. Enable the DHCP.
![10.10.2.0/24](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig02.png)
Then configure the **Port Forwarding** as follow:
![Port Forwarding](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig03.png)
You have to use **_exactly_** these ports:

|Name|Protocol|Host IP|Host Port|Guest IP|Guest Port| 
|---|---|---|---|---|---|
|ssh-jumphost|TCP||10022|10.10.2.254|22|
|ssh-rha-1|TCP||11022|10.10.2.101|22|
|ssh-rha-2|TCP||12022|10.10.2.102|22|
|ssh-rha-3|TCP||13022|10.10.2.103|22|

**2. Create your VMs**

Start with the **jumphost** (use any kind of Linux version, it doesn't matter, but it can help visually to change version between one and the other).
![jumphost](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig04.png)
1024 in memory size is enough.
![Memory](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig05.png)
Create a vitual disk.
![Virtual disk](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig06.png)
VDI is correct.
![VDI](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig07.png)
Dynamically allocated is better.
![Dynamically](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig08.png)
Set his size to 20GB to be comfortable.
![Size](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig09.png)
The first step is completed.
![Created](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig10.png)
Now go on setting, and Network. Choose the NAT Network previously created (eg. rancher-primer-lab)
![NAT Network](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig11.png)
In the storage tab, insert the iso.
![ISO](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig12.png)
The next step consist in cloning the first VM. The clone's name should be rha-1, and you have to reinitialize the MAC adresse.
![Clone](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig13.png)
Create a full clone.
![Full clone](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig14.png)
Repete the previous step for the two other VMs.
![all VMs](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig15.png)

**3. Install RancherOS**

Boot the first VM. As you can see in fig16, the DHCP set an IP adress for the host (eg. 10.10.2.4). Write down this adresse.
![IP](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig16.png)
Change/assign a password to the user rancher:

`$ sudo passwd rancher`
 
![password](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig17.png)
Add a temporary rule in port forwarding for this IP adresse:
![Add port](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig18.png)
You can now access to the VM over ssh with the password you set:

`$ ssh -p 22222 rancher@localhost`

![ssh](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig19.png)
Use the cloud-config file provided on Github for jumphost:

`$ vi cloud-config.yml`

Then paste the content of the file.
![cloud-config](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig20.png)
Install RancherOS with the following command:

`$ sudo ros install -c cloud-config.yml -d /dev/sda`

![ros](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig21.png)
Don't reboot immediatly the VM.
![Install](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig22.png)
Instead of that, use `$ sudo halt` to halt the VM.
![halt](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig23.png)
Then you can poweroff the VM.
![poweroff](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig24.png)
The iso isn't ejected automatically, so do it by hand.
![remove iso](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig25.png)
Then you can poweron the VM. As you could see, the IP adresse is the one we assign to it.
![poweron](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig26.png)
With the `~/.ssh/config` file provided on Github (set the perms to 0600 on the file), you can now normaly access the VM without password just by using the command:

`$ ssh jumphost`

![jumphost](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig27.png)

**4. Set up the new services**

Jumphost will run two services:

 - DNS (dnsmasq)
 - Database (MariaDB)

To set up the dns, use the following command:

```
$ docker run -d --restart=unless-stopped \
  -p 53/53/tcp -p 53/53/udp --cap-add=NET_ADMIN \
  de13/primer-dns
```

![dnsmasq](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig28.png)
Do the same with MariaDB:

```
$ docker run -d --restart=unless-stopped -p 3306:3306 \
  -e MYSQL_DATABASE=cattle \
  -e MYSQL_USER=cattle \
  -e MYSQL_PASSWORD=cattle \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
  mariadb:5.5
```

![jumphost](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig29.png)
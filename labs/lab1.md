# Lab1

## Network topology

```
                      +----------------+
                      |   VirtualBox   |
                      |  10.0.2.15/32  |
                      |    Rancher     |
                      +----------------+
```

## Info
This lab is planned to run on VirtualBox v5. The underlaying machine must have at least **2CPU** and **8Go Ram**.

The VM will run RancherOS v0.8.1. (OS installation is part of this lab.)

In the course material, you'll find the private key (`rancher-primer-lab.pem`) to access to VMs without password once they will be set up. [Download](https://github.com/de13/rancher-primer-lab/blob/master/keys/rancher-primer-lab.pem) the file in `~/.ssh/` and set rights to 0400.

The course material also provides you with a `config` file to falicitate access to VMs over `ssh`. Copy this file to `~/.ssh/config` on your computer, and **set perms to 0600** on the file. [Download](https://github.com/de13/rancher-primer-lab/blob/master/config-vbox) the file.

## Lab objectives

You'll first create a VM to install Rancher OS.

Once, install rancher sever.

And finally, add a host to Cattle to create your first environment.

## Create your Virtual Machine

Launch VirtualBox

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig01.png)

Create a new Linux machine

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig02.png)

Set the memory to 6Go

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig03.png)

Create a new virtual disk

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig04.png)

Use VDI

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig05.png)

Dynamically allocated is fine

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig06.png)

Set the size to 20Go

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig07.png)

The server is almost ready
![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig08.png)

Go to settings, then Storage tab to add your iso

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig09.png)

In Network tab, click on Port Forwarding

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig10.png)

Add these ports

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig11.png)

## Install rancher os

You can now boot the VM

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig12.png)

First, change rancher password

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig13.png)

Then connect to VM through ssh

```
$ ssh -p 22022 rancher@localhost
```

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig14.png)

Create a `cloud-config.yml` with the public key provided in the course material

```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5rrLFPwMsj2gCwRxaWhp3bqMaiieulEu9RuvUdr3Eqcq14py8rwdhB0L4hU7jM3I4/dMFb1/rObkni8eG5L8CMekRo4zet6T4EnKoW2eID/qQ18xa5/escDuRn0DFvz9vvXmJyc8IM2beAn4RCjLKVb4iKeh8Fmq7ntW+2loXVLFecNJQn10kBmIhgo4fLOZRmnLbABQoNCwPW2WmXjpkYkNZPW7+wOTKNCELyWOaBwlTjzLu3hdWVZgZHEzMLskELl96y//QVDKJ4Wif8KOqu5vKeP2lbsJ2Niq8/vWH94FqHeunE3LsEuzVHWEVtnTDMoUXz3PlVXixEEAv4z4X
hostname: rancher
```

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig15.png)

Install rancheros v0.8.1

```
$ sudo ros install -c cloud-config.yml \
  -d /dev/sda -i rancher/os:v0.8.1
```
![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig16.png)

Answer no to "Continue with reboot", and halt the server manually

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig17.png)

Power off the VM

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig18.png)

Remove iso from optical disk slot, then start the VM

![fig19](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig19.png)

## Install rancher server

Connect to the server through ssh

```
$ ssh -p 22022 -i rancher-primer-lab.pem rancher@localhost
```

![fig20](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig20.png)

Launch your first container: MariaDB

```
docker run -d -p 3306:3306 --restart=unless-stopped \
  -e MYSQL_DATABASE=rancher \
  -e MYSQL_USER=rancher \
  -e MYSQL_PASSWORD=rancher \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true mariadb:5.5
```

![fig21](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig21.png)

You can then have a look to logs to check if mariadb accept connections 

![fig22](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig22.png)

Once it's ready, check your ip address

```
$ ip a s eth0
```

You should have 10.0.2.15

![fig23](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig23.png)

It's time to start rancher/server

```
docker run -d --restart=unless-stopped \
  -p 8080:8080 rancher/server:v1.4.3 \
  --db-host 10.0.2.15 --db-port 3306 \
  --db-user rancher --db-pass rancher --db-name rancher
```

![fig24](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig24.png)

After a while, your server is ready, and you can connect to http://localhost:8080 with your browser

## Create your first environment

![fig25](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig25.png)

In the first tab, you can check your default environment is Cattle. Don't worry about the unhealthy status, it's because the first host is not yet registered

![fig26](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig26.png)

First step, in Admin tab and Settings, you have to set the server API address (clients use it to connect to the server). By default, Rancher offers the URL you use to connect to; but as we use NAT, it's not the correct one. Use http://10.0.2.15:8080 instead
 
![fig27](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig27.png)

Then, in Infrastructure tab, choose Add Host

![fig28](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig28.png)

You'll use the same host for the client than the server; so you have to specify the client address ip in the forth field

![fig29](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig29.png)

Copy the command, and past it in the VM command line

![fig30](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig30.png)

![fig31](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig31.png)

Close the Add Host page, and after a while, you'll see your client appears

![fig32](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig32.png)

All services start, and finally become ready

![fig33](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab1/fig33.png)

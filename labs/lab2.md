# Lab2

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

The first objective is to deploy Gitlab from the community catalog.

Then, you'll create your own catalog by using gitlab as your git repository.

You will create a resilient application (two nginx servers behind a load balancer), and the push it in your catalog.

This step completed, you'll remove your stack and deploy your application from your catalog. You'll then create a new version of your application, and update your stack using rancher update mechanisme.

## Deploy Gitlab

In order to use Gitlab, you need git; but git is not present on rancher os, and you cannot install any package in the default console.

You have to switch to an other console (anyone is correct but default). You'll use alpine, because it's the smallest one

```
$ sudo ros console switch alpine
```

Answer yes to contine

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig08.png)

Reconnect to the server, the welcome message warn you that you use alpine

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig09.png)

Update your package list

```
$ sudo apk update
```

Then install git

```
$ sudo apk add git
```

And finally create an ssh keypair for Gitlab

```
$ ssh-keygen -t rsa -P '' -f ~/.ssh.id_rsa
```

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig10.png)

Fine you can have a look on your public key and write it down in a text editor

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig11.png)

Back to your browser and choose Gitlab from community catalog

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig01.png)

Set up Hostname, URL as well as ports (eg. 3022 for ssh, 3080 and 3443 for http and https)

**WARNING!** Use **http** for the Gitlab external_url prefix instead of https.

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig02.png)

Click on Lauch

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig03.png)

Meanwhile the image pull, go to VirtualBox to configure new port redirection (eg. 3080 -> 18080)

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig04.png)

After a while, Gitlab is running

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig05.png)

First step in Gitlab (http://localhost:18080), change the root password

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig06.png)

Then login as root with your new password

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig07.png)

Create a new project called rancher-catalog

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig12.png)

Copy your id_rsa.pub key inside SSH Keys

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig13.png)

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig14.png)

Clone your repo on your host, and follow Gitlab instructions to initialize the repo

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig15.png)

## Create a resilient app

First, create an empty stack

![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig16.png)

Give her with a name

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig17.png)

Then add a service

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig18.png)

Choose to run 2 containers with de13/bluegreen:blue image

![fig19](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig19.png)

Wait until they are up and running

![fig20](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig20.png)

Then add a load balancer in front

![fig21](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig21.png)

Bind port 80 to port 80

![fig23](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig23.png)

Meanwhile you can add a port redirection in VirtualBox 80 to 8181 for example

![fig22](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig22.png)

Your stack is now ready

![fig24](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig24.png)

You can have a look http://localhost:8181

![fig25](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig25.png)

## Create a catalog entry

Go to config tab

![fig26](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig26.png)

On the host, go to `~/rancher-catalog` and create `templates/mycoolapp folders

![fig27](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig27.png)

In `~/rancher-catalog/templates/mycoolapp` create a `config.yml` file

```
name: Blue-Green Cool App
description: |
  A simple blue-green demo app
version: 1
category: Development
maintainer: de13 <stephane.beuret@data-essential.com>
```

Add alse an svg picture called `catalogIcon.svg` (you can use the one on Github in course material)

![fig28](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig28.png)

Create a 0 folder, and put in it `docker-compse.yml` and `rancher-compose.yml`

Then edit `rancher-compose.yml` to add catalog info

```
.catalog:
  name: "Blue-Green Cool App"
  version: 1
  description: "A simple blue-green demo app"
```

![fig29](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig29.png)

Add, commit and push with git

![fig30](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig30.png)

In Rancher settings, add a new catalog, and past your Gitlab URL (http://10.0.2.15:3080/root/rancher-catalog.git)

![fig31](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig31.png)

Then you can browse your catalog and see your app

![fig32](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig32.png)

Deploy your app from the catalog

![fig33](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig33.png)

Give a name to the stack

![fig34](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig34.png)

After a while your stack is ready

![fig35](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig35.png)

And you can check in the browser that it's the same than the one deployed by hand

![fig36](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig36.png)

## Create a new version of your app

Now, back to the CLI, in `~/rancher-catalog/templates/mycoolapp` copy the folder 0 to 1

`$ cp -r 0 1`

In `1/docker-compose.yml` change image de13/bluegreen:blue by de13/bluegreen:green

![fig37](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig37.png)

In `1/rancher-compose.yml` change the version to 2

![fig38](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig38.png)

Add, commit and push with git

![fig39](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig39.png)

After a catalog refesh, you could see the second version of your app

![fig40](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig40.png)

In the Stack tab, you should see there is an update available for your app

![fig41](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig41.png)

Click on Upgrade available, then Upgrade

![fig42](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig42.png)

Old containers will be replaced by new ones, with the new image

![fig43](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig43.png)

You can check in the browser that yoou have now the green app

![fig44](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab2/fig44.png)

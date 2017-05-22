# Lab6

## Network topology

```
                 rancherxx.data-essential.eu
                    +-------------------+
                    |     rancherxx     |
                    |   172.31.1.x/24   |
                    | rancher + mariadb |
                    +-------------------+

 lbxx.data-essential.eu
   +-----------------+  +-----------------+  +-----------------+
   |     hostxx-a    |  |    hostxx-b     |  |    hostxx-c     |
   |  172.31.1.x/24  |  | 172.31.50.x/24  |  | 172.31.100.x/24 |
   |    front-end    |  |    back-end 1   |  |   back-end 2    |
   +-----------------+  +-----------------+  +-----------------+
```
Where `xx` is your student number (eg. 01, 02 and so on)

## Info

This lab is planned to run on AWS. All ec2 are ready to use. Each student should use the config file (`~/.ssh/config`) provided in the course material and `.pem` file provided by the instructor to access to hosts over ssh.

`$ ssh host01-a`

Rancher UI will be available through http://rancherxx.data-essential.eu:8080 one rancher/server will be installed (rancher/server install is part of this lab).

## Lab goals

This short lab will demonstrate how easy it is to upgrade rancher/server

## Upgrade rancher/server

In the bottom left corner of Rancher UI, you can check the current version of your server

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig01.png)

On the dockerhub page of rancher/server, in the tag tab, you can check existing release for the product; as you can see, v1.5.10 was released 3 days ago, and seems to be the latest and stable version (rancher/server:v1.6.0 also exists). So you'll install v1.5.10

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig02.png)

First think to do, remove the old version from the rancher host

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig03.png)

Then launch the new one with the same paramaters than previously

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig04.png)

In the logs, you can see the DB migration

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig05.png)

After less than 2 minutes, rancher UI is available again. Check the version in the bottom left corner. v1.5.10 amazing

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig06.png)

Some system services could be manually updated, but everything is still running

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig07.png)

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab6/fig08.png)

Congrats! that all, and remember that during this update, your environment is still u and running
# Lab8

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

In this lab, you will 

 - Install and configure EBS Rancher Driver plugin.
 - Create a volume with rancher-ebs driver
 - Use this volume in a simple container
 - Re-use the volume in a second container

## Install and configure EBS Driver

Go to Catalog Library, and install Rancher EBS

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig02.png)

It takes only few second to be up

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig03.png)

Then, go to Infrastructure tab, then Storage

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig04.png)

Looks good, it's time to add a volume

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig05.png)

## Use and re-use a volume

When you create an EBS volume, you have to give it a name, and at least a size

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig06.png)

Refer to documentation page to see all other options

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig18.png)

So, the volume is now created, but not used

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig07.png)

Create a single container, and add a volume

`volume-name:/path/on/the/host`

And for the volume driver name: `rancher-ebs`

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig08.png)

It could take a while (not too long, but a bit more longer than usual) to create the container with the volume for the first time, because the volume needs to be create on AWS first

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig09.png)

Open a terminal on the container to check if the volume is attached

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig10.png)

Let's create an `index.html` file in the volume

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig11.png)

It's now time to remove this first container

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig12.png)

As you can see, volume is now in Detached state

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig13.png)

Let's create a new nginx container, an let's mount the volume on `/usr/share/nginx/html/`

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig14.png)

This time, the container comes faster

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig15.png)

In the storage tab, yoou can see the volume mounted on the container

![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig16.png)

Install `curl` and try to perform `curl localhost` to see `index.html` file you previously created!

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab8/fig17.png)



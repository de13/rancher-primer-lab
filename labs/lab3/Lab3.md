# Lab3

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

You'll first install mariadb and rancher/server, and deploy a Kubernetes environement, and then replace Kubernetes by Cattle.

You will apply tags on your hosts, and deploy an app using this tags.

## Install rancher/server

You already know how to perform the following tasks

 - install mariadb
 - install rancher/server
 - connect to rancher with you browser

Install mariadb

```
$ docker run -d -p 3306:3306 --restart=unless-stopped \
  -e MYSQL_DATABASE=rancher \
  -e MYSQL_USER=rancher \
  -e MYSQL_PASSWORD=rancher \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true mariadb:5.5
```

Install rancher/server

```
docker run -d --restart=unless-stopped \
  -p 8080:8080 rancher/server:v1.4.3 \
  --db-host 172.31.1.xx --db-port 3306 \
  --db-user rancher --db-pass rancher --db-name rancher
```

Connect to rancher UI

http://rancherxx.data-essential.eu:8080

## Create an environment and add hosts

On the first tab, choose Manage Environments, then Add Environment

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig01.png)

Give a name to this environment, and use Kubernetes template, then create environment

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig02.png)

You can see a new Kubernetes environement active

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig03.png)

Switch to this environment

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig04.png)

Kubernetes is setting up, so it's time to add some hosts

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig05.png)

First, set up the host registration URL (check on `rancherxx` your address ip): http://172.31.1.xx:8080 

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig07.png)

Go to infrastructure tab, and choose add host

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig06.png)

Just copy the register command...

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig08.png)

...and paste it to your `hostxx-a`, `hostxx-b`, `hostxx-c`

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig09.png)

You can see your host registering

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig10.png)

After a while, your Kubernetes environement is ready

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig11.png)

## Create an environment with Cattle

But we will not work on Kubernetes... so to remove the environmnet, you have to deactivate it first

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig12.png)

Once it's done, you can delete it

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig13.png)

Remove the environement doesn't remove the containers on the hosts, so you have to do it manually on each host. Use the following command (twice):

`$ docker ps -aq|xargs docker rm -vf`

`docker ps -aq` print only the CONTAINER ID of running and stopped containers; `docker rm -fv` remove the container, even if it's a running one, and all volumes

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig14.png)

Have a look to verify if there no more container on the hosts

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig15.png)

Now, in the default environement, add the new hosts: copy the command and paste it on hosts

![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig16.png)

You can see the hosts registrering and new services starting

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig17.png)

## Use tags

On the first host, on the top right, use edit

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig18.png)

Add a label in the form of key=value, `name=frontend` on `hostxx-a`, `name=back-end`, `zone=b` on `hostxx-b` and `name=back-end`, `zone=c` on `hostxx-c`

![fig19](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig19.png)

You can see the tag on your host

![fig20](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig20.png)

Once it's done, it looks like this

![fig21](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig21.png)

So, it's time to add scheduling rules to your application

![fig22](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig22.png)

Add your catalog

![fig23](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig23.png)

Create a new version of the app in the catalog

`$ cp -r 1 2`

Edit `docker-compose.yml`

Add the following labels to nginx:

```
io.rancher.scheduler.affinity:host_label: name=back-end
io.rancher.scheduler.affinity:container_soft: zone=b
io.rancher.scheduler.affinity:container_soft_ne: zone=b
```

The first, to force containers to run on hosts with `name=back-end` label
The second, the container should run on hosts with label `zone=b`
And the third, the container should not run on the same host than another nginx container
So with these labels, the two nginx servers should not run on the same hosts, but must run on a back-end host

It's more simple for the load balancer, because you just want to run on the host with `name=front-end` label

```
io.rancher.scheduler.affinity:host_label: name=front-end
```

Edit `rancher-compose.yml` to increment the version

![fig24](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig24.png)

Commit and push the new version

![fig25](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig25.png)

You should see the new version of your app

![fig26](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig26.png)

Verify your files in preview

![fig27](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig27.png)

Once deployed, your app should looks like this

![fig28](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab3/fig28.png)

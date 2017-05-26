# Lab5

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

In this liab you'll install and set up Prometheus

## Install Prometheus

You already know how to install an app from the catalog using the UI or CLI; choose your favorite method for Prometheus

**WARNING**: we are willingly running in `rancher/server:v1.4.3` for lab6 requirement. So, even if the CLI shows us Prometheus:3.0.0, this version requires `rancher/server:v1.5.5`; use `2.0.0` instead

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig01.png)

You can see Prometheus deploiment 

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig02.png)

Containers are randomly splited between the hosts

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig03.png)

And finally everything is up and running

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig04.png)

You can have a look on the stack composition, and notice there is a lot of services and containers

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig05.png)

But to use Prometheus, you need to access to prometheus service on port 9090

If you have a look on the network topology, you noticed only `hostxx-a`, which is the front end server, is reachable from the internet... But unfortunately, prometheus container is deployed randomly, and not necessarly on this hosts

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig06.png)

Looks like you have to create your custom prometheus. To solve the issue, you have two main choices:

 - Create a load balancer
 - Constrain prometheus to run on a specific host

For this lab, let's try the second

Under `rancher-catalog/templates`, create `my-prometheus/0`

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig07.png)

Copy `docker-compose.yml` and `rancher-compose.yml` from your deployed app

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig08.png)

Go to `the rancher/community-catalog` on Github

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig09.png)

Find the templates entry for Cattle templates

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig10.png)

Find Prometheus

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig11.png)

Save the catalogIcon.svg under `my-prometheus` forlder. Copy the content of `config.yml` and create your custom one

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig12.png)

Update `rancher-compose.yml` with catalog info

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig13.png)

You can change the version to 1.0.0

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig14.png)

You can remove all unecessary parts

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig15.png)

In docker-compose.yml, add the scheduling rule for prometheus service

![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig16.png)

Commit your changes in your catalog, then push them to yoour git repo

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig17.png)

Once you have refreshed your catalog, you should see your new app

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig18.png)

Only one version as expected

![fig19](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig19.png)

Remove the Prometheus stack you previously deployed

![fig20](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig20.png)

Then from the catalog, add the custom one

![fig21](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig21.png)

Now prometheus container MUST run on `hostxx-a`

![fig22](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig22.png)

You can then access it through http://lbxx.data-essential.eu:9090

![fig23](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig23.png)

It's nice, but to see your monitoring graph, you also need Grafana... But Grafana runs on a random host (looks like a familliar issue)

![fig24](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig24.png)

So it's your challenge! Create a new version of my-prometheus in the catalog to constraint grafana container to run on `hostxx-a`

![fig25](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab5/fig25.png)
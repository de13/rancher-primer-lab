# Lab7

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

 - Enable Network Policies
 - Manage policies from the UI
 - Manage policies from the API

## Enable Network Policies in Rancher

Go to Environment tab (Default in your case), and then Manage Environements

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig01.png)

You can see that Network Policy require a template to be deployed

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig02.png)

In Catalog - Library, choose Rancher Network Policy Manager

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig03.png)

Deploy the last version

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig04.png)

It's not takes a while to be ready

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig05.png)

## Manage policies from the UI

If you back to Manage Environement, then Default, you can now see that Network Policy is available

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig06.png)

By default all the rules are configured to allow traffic

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig07.png)

So change some rule to see what happens in the environement

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig08.png)

See your containers IP, and do some tests

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig09.png)

Check if your application is still available

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig10.png)

Check if you can acces to container IP of a foreign stack

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig11.png)

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig12.png)

## Manage policies from the API

You have to use `http://<RANCHER_SERVER_IP>/v2-beta/projects/<PROJECT_ID>/networks/<NETWORK_ID>` where the `<NETWORK_ID>` is the id of ipsec network

![fig13](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig13.png)

If you click on Edit, you see this window

![fig14](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig14.png)

You may change a parameter manually, replacing allow by deny, just like in the UI

![fig15](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig15.png)

When you click on Show Request, you'll see the `curl` request to apply your change.

![fig16](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig16.png)

In the UI, put all Policies to allow

![fig17](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig17.png)

Change the linked Services from allow to deny in the Edit page. You can chose to send request from the browser

![fig18](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig18.png)

Or to past it in a CLI

![fig19](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig19.png)

Once it's done, you can check the change in the UI

![fig20](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab7/fig20.png)
# Lab4

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

In this lab, you'll learn how to user `Rancher CLI`

 - Download `Rancher CLI`
 - Configure `Rancher CLI` with your end point and API key
 - Use `Rancher CLI` to list resources
 - Use `Rancher CLI` to deploy an app

## Download and configure your CLI

In the right down corner of Rancher UI, you find a link to download the CLI.

![fig01](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig01.png)

Second step to use the CLI, create an API Key. Go to the API tab. Write down the Endpoint URL (try the v2-beta)

![fig02](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig02.png)

Add an account API key

![fig03](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig03.png)

Write down Access Key and Secret Key (notice the warning about the secret key, if you loose it you have to generate a new one)

![fig04](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig04.png)

## Use Rancher CLI

On the command line, use `rancher config` to configure `Rancher CLI` with the Endpoint URL, the Access Key and the Secret Key

![fig05](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig05.png)

You can try different command to gather data on your configuration; for example, try these ones:

 - `rancher env ls`
 - `rancher ps`
 - `rancher ps -c`
 - `rancher hosts`
 - `rancher --host hostxx-a ps`

![fig06](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig06.png)

And finally `rancher --help`

![fig07](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig07.png)

## Install an app from the CLI

Try `rancher catalog ls|grep -i blue` to see your app

![fig08](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig08.png)

Remove the old one (from the UI or the CLI), and reinstall it with the CLI

`$ ./rancher catalog install my-catalog/bluegreen:3`

![fig09](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig09.png)

From the CLI, verify your stack is correctly running

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab4/fig10.png)

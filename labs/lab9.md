# Lab9

## Network topology


```
                            server
                    +-------------------+
                    |  rancher/server + |
                    |     mariadb +     |
                    |   rancher/client  |
                    +-------------------+

           +-----------------+  +-----------------+
           |     client1     |  |    client2      |
           |  rancher/client |  |  rancher/client |
           |                 |  |                 |
           +-----------------+  +-----------------+
```

## Info

This lab is planned to run on AWS. All ec2 are ready to use. Each student should use the config file (`~/.ssh/config`) provided in the course material and `.pem` file provided by the instructor to access to hosts over ssh.

`$ ssh host01-a`

Rancher UI will be available through http://rancherxx.data-essential.eu:8080 one rancher/server will be installed (rancher/server install is part of this lab).

## Lab goals

A distributed system is designed for failure.

Even if our rancher/server is not in HA mode (we need at least 2 rancher/server for that), the environment we've created is highly available.

So perform some [chaos engineering](http://principlesofchaos.org/)!

Deploy and configure a resilient App, and experience it againt:

- Container failure
- Host failure
 
Learn to perform Rancher Database backups and restores

## Install myApp

MyApp is a resilient Node.js app, with a MongoDB backend. For persistence, MongoDB use a rancher-ebs driver.

```

           +-----------------+  +-----------------+
           |                 |  |                 |
           |  +-----------+  |  |  +-----------+  |
           |  |   LB/RP   |  |  |  |   LB/RP   |  |
           |  +-----------+  |  |  +-----------+  |
           |                 |  |                 |
           |  +-----------+  |  |  +-----------+  |
           |  |  nodejs   |  |  |  |  nodejs   |  |
           |  +-----------+  |  |  +-----------+  |
           |                 |  |                 |
           |  +-----------+  |  |                 |
           |  |  mongodb  |  |  |                 |
           |  +-----------+  |  |                 |
           |      _____      |  |                 |
           |     (_____)     |  |                 |
           |     | ebs |     |  |                 |
           |     |_____|     |  |                 |
           |                 |  |                 |
           +-----------------+  +-----------------+
```

### Note:

In this configuration MongoDB is not in HA, the EBS backend only ensure data persistence.

Add [https://github.com/saphoooo/rancher-catalog.git](https://github.com/saphoooo/rancher-catalog.git) as a Catalog.

Install myApp from the catalog.

![fig1](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig1.png)

Configure a Load Balancer stack to access your app from outside.

![fig2](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig2.png)

Ensure your configuration is well configured and works fine.

![fig3](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig3.png)

Now, delete or stop one Node.js container. What happens? How long did Cattle takes to recover you application? Is your application always reachable?

![fig4](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig4.png)

Now, stop the node where MongoDB is not running (so only Node.js). What happens? How long did Cattle takes to recover you application? Is your application always reachable?

## Backup and Restore Rancher's Database


### Backup

Did you notice that you can remove every piece of the Rancher puzzle without breaking it? It always recovers from failures. But the database is the master piece. You must be sure to use an high available configuration (MariaDB HA or Galera). Futhermore you must perform backups on regular basis (once a day at least). If everything goes wrong, yoou can recover your entire environment from your database.

First, you should connect to your server host.

```ssh server```

Next, find your mariadb container's id.

```docker ps|grep -i mariadb```

Create a dump of your database.


```
docker exec -i ${container_id} mysqldump -ucattle -pcattle \
  --databases cattle > rancher.dump
```

From your local conputer, copy your dump.

```scp server:~/rancher.dump .```

Your runtime environment is now safe!

### Chaos!

It's time to break everything!

Go on all hosts (server + clients) and perform the following command:

```docker ps -aq|xargs docker rm -f```

Bad news, you have totaly lost your environment, go back to lab1, an recreate everything by hand.

![fig7](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig7.png)

### Restore

You have first to create the database, so run a MariaDB container.

```
docker run -d -p 3306:3306 --restart=unless-stopped \
   -e MYSQL_DATABASE=cattle -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
   -e MYSQL_USER=cattle -e MYSQL_PASSWORD=cattle \
   mariadb:10.3
```

Push your dump on your server.

```scp rancher.dump server:.```

Restore your database.

```
docker exec -i ${mariadb_container_id} mysql -ucattle \
  -pcattle cattle < rancher.dump
```

Now that your database is ready, run rancher/server.

```
docker run -d --restart=unless-stopped \
  -p 8080:8080 rancher/server --db-host ${internal_host_ip} \
  --db-port 3306   --db-user cattle --db-pass cattle \
  --db-name cattle
```

Once the server if ready, connect to the Web UI.

Ouch!

![fig5](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig5.png)

Mmmh look like our clients are dead too! Use Add Host to boostrap new agents.

What happens???

![fig6](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig6.png)

It looks like magic, all my containers come back from the dead! You think it's Halloween or you begin to be confident with containers?

And, you can notice that myapp is working fine as well.

![fig6](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab9/fig3.png)
# Lab10

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

`$ ssh server`

Rancher UI will be available through http://rancherxx.data-essential.eu:8080 one rancher/server will be installed (rancher/server install is part of this lab).

## Lab goals

You remember myApp? It was nice to have a full stack deployed by clicking only on one button... But then, you have to go to your LB/RP stack to add a route to expose publicly your app, and it's a kind of operational overhead that we would like to avoid. How?

In this lab you'll deploy Traefik, a Cloud Native Reverse Proxy/Load Balancer.

You'll then how to automatically configure your routes when deploying your app.


## myApp Architecture (20000ft)

```
           +-----------------+  +-----------------+
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

## Deploy Traefik

If you still have a load-balancer stack, it's time to remove it.

![fig1](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig1.png)

Then, you'll fiind Traefik in the Community Catalog.

![fig2](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig2.png)

Well for the moment, the stack is unhealthy.

![fig3](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig3.png)

You need to label some hosts (at least one) with `traefik_lb=true`

![fig4](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig4.png)

So let's label!

![fig5](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig5.png)

Once your hosts are labeled, Traefik is deployed on them.

![fig6](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig6.png)

## Let's perform some magic

First, collect myapp `docker-compose.yml` and `rancher-compose.yml`

![fig7](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig7.png)

Unzip the file, and your file are there.

![fig8](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig8.png)

Remove then the myapp stack.

![fig9](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig9.png)

Edit `docker-compose.yml` to add specific labels for Traefik.

```
  myapp:
    image: de13/myapp
    stdin_open: true
    tty: true
    links:
    - mongo-myapp:mongodb
    labels:
      io.rancher.container.pull_image: always
      traefik.frontend.rule: 'Path:/myapp'
      traefik.port: '3000'
      traefik.enable: 'true'
```

We added 3 labels:

* `traefik.enable: 'true'` to use Traefik.
* `traefik.port: '3000'` to warn Traefik about our app port.
* `traefik.frontend.rule: 'Path:/myapp'` to warn Traefik about our route.

Now, let's deploy the new stack.

![fig10](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig10.png)

And, once deployed, I can directly check in my browser for my app (on port 8080, it's the defined port for Traefik).

![fig11](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig11.png)

You can have a look at Traefik administrative console.

![fig12](https://s3-eu-west-1.amazonaws.com/data-essential-rancher-primer-lab/lab10/fig12.png)

Figure out what it means! No more ops overhead! A reduced risk of human error! And over all, a fully controlled automated deployment!

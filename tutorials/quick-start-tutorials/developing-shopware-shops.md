# Developing Shopware Shops

Building full Shopware shops is really easy with Docker and  dockware images.\
Here are a few easy steps to get started.

## Environment Strategy

The first thing you want to clarify is, what **type of infrastructure** your project needs.\
Are you happy with a **single container**, or do you want to **reproduce the production system** which usually consists of **multiple services**, or (at least) a separate database instance.

If you are happy with a single container, we recommend using the **dockware/shopware-essentials** image, which brings all tools such as MySQL out of the box. You only need to connect into the container and install your preferred Shopware version.

If you have more serious setups, we recommend using the plain **dockware/web** image.\
This one is made to be a pure and easy development environment for PHP and Node based applications. Compared to the shopware-essentials image, this one does not come with any additional toolings and is a clean container only.

## Create Environment

We decided on using a more complex setup with additional services.\
So start by creating a **docker-compose.yml** file.

We build a container for our **Shopware shop**, an additional **MySQL container** including persisting of the data, and a separate container for a **Symfony based application** that moves data from the ERP system to Shopware by using the Shopware API (just a fictional sample).

To use domains, we add an additional **NGINX based proxy**. We use it to forward HTTP requests, but also TCP requests to the database (could be done directly by exposing the port of the MySQL container, but maybe you can learn something haha).

```yaml
proxy:
  image: dockware/proxy:latest
  container_name: proxy
  ports:
    - 80:80
    - 443:443
    - 3306:3306
  volumes:
    - "../proxy/shopware.conf:/etc/nginx/conf.d/shopware.conf:ro"
    - "../proxy/middleware.conf:/etc/nginx/conf.d/middleware.conf:ro"
    - "../proxy/tcp.conf:/etc/nginx/conf.stream.d/tcp.conf:ro"
  
shopware:
  image: dockware/web:latest
  container_name: shopware
  volumes:
    - "./shop/src:/var/www/html"
 
middleware:
  image: dockware/web:latest
  container_name: middleware
  volumes:
    - "./middleware/src:/var/www/html"
    
db:
  image: mysql:8.4.0
  container_name: mysql
  volumes:
    - "db_vol:/var/lib/mysql"
  environment:
    - MYSQL_ROOT_PASSWORD=xxx
    - MYSQL_PASSWORD=xxx
    - MYSQL_DATABASE=shopware
    - TZ=Europe/Berlin
      
volumes:
  db_volume:
    driver: local
```

The only thing that is left, before we can start our environment is the 3 NGINX configuration files.

{% tabs %}
{% tab title="shopware.conf" %}
```nginx
server {
    listen        80;
    server_name   shopware.my-domain.com;
    return 301    https://$host$uri$is_args$args;
}

server {
    listen        443 ssl;
    server_name   shopware.my-domain.com;

    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    location / {
        proxy_pass https://shopware;
    }
}
```
{% endtab %}

{% tab title="frontend.conf" %}
```nginx
server {
    listen        80;
    server_name   middleware.my-domain.com;
    return 301    https://$host$uri$is_args$args;
}

server {
    listen        443 ssl;
    server_name   middleware.my-domain.com;

    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    location / {
        proxy_pass https://middleware;
   }
}
```
{% endtab %}

{% tab title="tcp.conf" %}
```nginx
server {
    listen 3306;
    proxy_pass db:3306;
}
```
{% endtab %}
{% endtabs %}

Now just edit your local **/etc/hosts** file and add entries for your domains.

```
127.0.0.1     middleware.my-domain.com
127.0.0.1.    shopware.my-domain.com
```

{% hint style="success" %}
If you want to use a more dynamic way instead of using /etc/hosts, this blog post could be interesting for you: [https://www.boxblinkracer.com/blog/docker-dnsmasq](https://www.boxblinkracer.com/blog/docker-dnsmasq)
{% endhint %}

And that's it.\
Start your Docker environment and you should be able to access **https://middleware.my-domain.com** and **https://shopware.my-domain.com.** Of course, these will lead to empty web directories.

```bash
docker-compose up -d
```

{% hint style="info" %}
If your browser shows invalid certificates, just continue (in Chrome just type "thisisunsafe").\
With Shopware, keep in mind, your Storefront is only available if you create a dedicated domain entry in your sales channel - but you actually just need the Administration anyway.
{% endhint %}

## Create  Shopware and Middleware

It's time to start with our projects.\
This tutorial will not cover that in detail.

Just connect into the container and start the installation. The files will be automatically available in your **src** folders on your host system.

```bash
docker exec -it shopware bash
```

When you need to access either the MySQL database internally in the Docker network, or another container such as Shopware, please keep in mind to use the key names of the yaml files.

The database connection string would look like this

```bash
DATABASE_URL=mysql://root:root@db:3306/shopware
```

{% hint style="success" %}
That's it, you now have a working development environment with a pretty cool infrastructure that can easily be improved with way more containers and features.
{% endhint %}

## Taking care of the database

We have a database being **persisted by using Docker volumes**.\
This means, whenever we restart our environment, the database is existing again, as long as we have not pruned our Docker volumes.

Why do we do this?&#x20;

When we create full Shopware shops, the demo data is not sufficient because our clients’ shops need to be **tailored to their specific requirements**, which means extensive configuration in the **database**.\
In addition to this, we usually work together as a team, which means everyone need to have access to the same kind of data(base).

There are different ways to tackle this problem. In theory, you could use **migrations** or **bash scripts** to configure everything—either via Shopware’s **bin/console commands** for system settings or directly through **MySQL queries**. This approach works, and best of all, it’s reproducible.

A different approach is, to share a (pseudo-anonymized) database across your team.\
Let's imagine you have a **shopware.sql.tar backup** in your Docker folder (GIT excluded!!!)

You could easily have a **setup script** or **makefile** with a target like `make run` that imports the database in your running enviroment. If it takes too long, maybe you don't need to import it on every startup - it's persisted anyway - so let the developers just import it on demand, etc.

Here is a script that copies the backup into the container, unzips it and imports it into MySQL.&#x20;

```bash
# copy into container and unzip
docker cp ./shopware.sql.tar db:/tmp/shopware.sql.tar
docker exec -i db bash -c "cd /tmp && tar xfv shopware.sql.tar"
# -------------------------------------------------------------------------
# disable checks and import
docker exec -i db bash -c "mysql -uroot -proot -e \"SET AUTOCOMMIT = 0; SET UNIQUE_CHECKS = 0; SET FOREIGN_KEY_CHECKS = 0;\""
docker exec -i db bash -c "mysql -uroot -proot --max_allowed_packet=1GB -e \"use shopware; source /tmp/shopware.sql;\""
docker exec -i db bash -c "mysql -uroot -proot -e \"SET FOREIGN_KEY_CHECKS = 1; SET UNIQUE_CHECKS = 1; SET AUTOCOMMIT = 1; COMMIT;\""
# -------------------------------------------------------------------------
# delete raw file again in container
docker exec -it db bash -c "rm -rf /tmp/shopware.sql"
```

These steps give you a powerful approach to collaborate with a combination of source code and database, to create the best Shopware shops for your clients.

## Where to go from here?

Depending to the complexity of your project, you might require additional things such as new **containers**, **installation scripts** to automatically install dependencies and more.

This is not really part of dockware, because it's really bound to your special needs.\
The great thing is, it is all **just plain Docker**, so you can basically build and connect everything you need.

Here are a few typical things you usually want in your projects

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>HTTP and SSL</td><td>Use HTTPS instead of HTTP for your projects</td><td><a href="../docker-tutorials/domains-with-https.md">domains-with-https.md</a></td></tr><tr><td>Improve Bind-Mounting</td><td>Optimize performance and structure when it comes to bind-mounting your source code</td><td><a href="../docker-tutorials/correct-bind-mounting.md">correct-bind-mounting.md</a></td></tr><tr><td>Web Image Features</td><td>Explore the features of dockware/web to improve your development envrionment.</td><td><a href="../../dockware-web/features.md">features.md</a></td></tr></tbody></table>

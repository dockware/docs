# Multi-Environment Setups

Environments with multiple shops are indeed easy.\
In fact, the only important thing you need to take care of, is the correct routing.

Docker is routing based on the **domain** and **port** that you configure.\
You can either use different ports for your shops or use a full reverse proxy to make it even easier to use it.

{% hint style="info" %}
This tutorial will use a NGINX proxy for the routing, but always remember, you can also just use different ports like "1000:80", "1001:80" for your containers, or things like [https://traefik.io/](https://traefik.io/)....
{% endhint %}

## Important to know

You can of course combine this approach with all other Docker images.\
So maybe you want a single MySQL image for all shop databases, or an Elasticsearch that all containers share? Feel free to build whatever you need!

That is the part where dockware is nothing more than a super cool Docker image with everything installed that you need to run Shopware. In that case you might even want to go with a **dockware/essentials** or **dockware/flex**.

The tutorial below will however focus on using the **dockware/dev** images that come with a full Shopware already installed. This means that we only need a single container for every shop that should be running. For a lot of people, this might be an easier approach, but still keep in mind - **only your creativity limits your setups**.

Both examples can be found in our[ example Github repository](https://github.com/dockware/examples):

| Example             | Description                                                        | Link                                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| dockware/dev        | Pre-Installed Shopware 6, ready to be used                         | [https://github.com/dockware/examples/tree/master/multi-environments/dev](https://github.com/dockware/examples/tree/master/multi-environments/dev)               |
| dockware/essentials | 2 shops with custom installation and slimmer architecture (images) | [https://github.com/dockware/examples/tree/master/multi-environments/essentials](https://github.com/dockware/examples/tree/master/multi-environments/essentials) |



## Example with dockware/dev

### 1. docker-compose.yml

We start with our docker-compose.yml file.\
It contains a **dockware/proxy** image as well as **2 dockware/dev** images.\
\
While the main ports are only exposed through our proxy, both Shopware instances allow you to access their data with custom ports (SFTP + MySQL).\
_You can of course use bind-mounting and other approaches for this._

The proxy has 2 configuration files that will be available with bind-mounting.\
These contain the full logic for the routings to our containers. (see next section).

<details>

<summary>Template: docker-compose.yml</summary>

```ruby
version: "3.8"

services:

  proxy:
    container_name: proxy
    image: dockware/proxy:latest
    ports:
      - "80:80"
      - "443:443"
      - "8888:8888"
      - "9999:9999"
      - "9998:9998"
    depends_on:
      - shop1
      - shop2
    volumes:
      - "./proxy/shop-1.conf:/etc/nginx/conf.d/shop-1.conf"
      - "./proxy/shop-2.conf:/etc/nginx/conf.d/shop-2.conf"
  # -----------------------------------------------------------------------
  shop1:
    image: dockware/dev:6.4.11.1
    container_name: shop1
    ports:
      - "2001:22"
      - "3001:3306"
  shop2:
    image: dockware/dev:6.4.11.1
    container_name: shop2
    ports:
      - "2002:22"
      - "3002:3306"
```

</details>

### 2. NGINX Configuration

Both shops get custom NGINX configuration file that are available in our proxy thanks to bind-mounting. You can do all kinds of fancy things in here.\
\
Below is a sample that ensures that **all traffic for shop1.shopware.com is routed though HTTPS 443 to your container**. It will also use the embedded self-signed certificates for SSL.

In addition to this, the sections below that routing help you to make the **watchers** work with your custom domain. You only need them if you want to use the watchers.

The watcher for the **administration** needs a few location settings that mainly focus on the port that is used for forwarding (8888 or 80).

The **storefront** watcher only listens to the required port 9998 and passes it on.\
At the moment, the pre-configured storefront watcher does only work with **LOCALHOST**.\
I haven't had any time to changes this in Shopware/dockware, so all requests will come in as localhost within your shop container (**proxy\_**_**set**_**\_header**). This means you only need to have **http://localhost in your Sales Channel** and you're good to go :)

{% hint style="warning" %}
The most important thing here, don't forget the correct **proxy\_pass** setting.\
This needs to be your Docker container name, like http(s)://{containerName\}}.\
That will forward the traffic to this container.
{% endhint %}

<details>

<summary>Template: NGINX Configuration Shop 1</summary>

```bash
server {
    listen        80;
    server_name   shop1.shopware.com;
    return 301    https://$host$uri$is_args$args;
}

server {
    listen        443 ssl;
    server_name   shop1.shopware.com;

    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    location / {
        proxy_pass https://shop1;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
    }
}

server {
    listen                    8888 ssl;
    server_name               shop1.shopware.com;

    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    location /admin {
        proxy_pass            http://shop1:8888;
        proxy_next_upstream   error timeout invalid_header http_500 http_502 http_503 http_504;
    }
    location /static {
        proxy_pass            http://shop1:8888;
        proxy_next_upstream   error timeout invalid_header http_500 http_502 http_503 http_504;
    }
    location /api {
        proxy_pass            http://shop1;
        proxy_next_upstream   error timeout invalid_header http_500 http_502 http_503 http_504;
    }
    location / {
        proxy_pass            http://shop1:8888;
        proxy_next_upstream   error timeout invalid_header http_500 http_502 http_503 http_504;
    }
}

server {
    listen                    9998;
    server_name               shop1.shopware.com;

    location / {
        proxy_set_header      Host    localhost;
        proxy_pass            http://shop1:9998;
        proxy_next_upstream   error timeout invalid_header http_500 http_502 http_503 http_504;
    }
}
```

</details>

### 3. /etc/hosts File

To tell your host system that shop1.shopware.com and shop2.shopware.com do indeed exist, please modify your **/etc/hosts** file on your host.

_This needs only been done once._

Just use this snippet to configure, that all lookups for these 2 domains are resolved by simply pointing to localhost.

```
127.0.0.1     shop1.shopware.com
127.0.0.1     shop2.shopware.com
```

### 4. It's a wrap

That's it. If you now start your Docker containers, it should all work.

The only thing you need to configure, is the correct domains in your Sales Channels of both shops. Just head to **https://shop1.shopware.com/admin** and set the **domain for your Storefront Sales Channel**. Do this for both shops.

Our Github example shows you how to do this automatically within a makefile :)

{% hint style="success" %}
If you want to see a more convenient plug'n'play way with our makefiles, head to this page for more: [https://github.com/dockware/examples/tree/master/multi-environments/dev](https://github.com/dockware/examples/tree/master/multi-environments/dev)
{% endhint %}




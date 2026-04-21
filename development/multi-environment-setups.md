# Multi-Environment Setups

Environments with multiple shops are indeed easy.\
In fact, the only important thing you need to take care of, is the correct routing.

Docker is routing based on the **domain** and **port** that you configure.\
You can either use different ports for your shops or use a full reverse proxy to make it even easier to use it.

{% hint style="info" %}
In these tutorials we will use an NGINX proxy for the routing in the first part, and [Traefik](https://traefik.io/) for a second, but always remember, you can also just use different ports like "1000:80", "1001:80" for your containers, or any other reverse proxy.
{% endhint %}

## Important to know

You can of course combine this approach with all other Docker images.\
So maybe you want a single MySQL image for all shop databases, or an Elasticsearch that all containers share? Feel free to build whatever you need!

That is the part where dockware is nothing more than a super cool Docker image with everything installed that you need to run Shopware. In that case you might even want to go with a **dockware/essentials** or **dockware/flex**.

The tutorials below will however focus on using either the **dockware/dev** or **dockware/play** images that come with a full Shopware already installed. This means that we only need a single container for every shop that should be running. For a lot of people, this might be an easier approach, but still keep in mind - **only your creativity limits your setups**.

Both examples can be found in our[ example Github repository](https://github.com/dockware/examples):

| Example             | Description                                                        | Link                                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| dockware/dev        | Pre-Installed Shopware 6, ready to be used                         | [https://github.com/dockware/examples/tree/master/multi-environments/dev](https://github.com/dockware/examples/tree/master/multi-environments/dev)               |
| dockware/essentials | 2 shops with custom installation and slimmer architecture (images) | [https://github.com/dockware/examples/tree/master/multi-environments/essentials](https://github.com/dockware/examples/tree/master/multi-environments/essentials) |



## Example with dockware/dev and nginx

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

## Example with dockware/play and Traefik

### 1. Prerequisites and Traefik configuration

All the ins and outs of Traefik configuration are beyond the scope of this tutorial. We assume you have a dedicated host to deploy your setup on, which also is reachable from Let's Encrypt if you want to use TLS certificates. Additionally, you'd need to have a wildcard DNS record, meaning `*.stage.yourdomain.invalid` points towards the IP address of the host you're using.

It's easy to use `docker-compose` to spin up a Traefik instance. Create a directory `traefik` wherever you'd like to collect your services (e.g. `~/services/traefik`), and create the following `docker-compose.yml`:

<details>

<summary>docker-compose.yml for a basic Traefik</summary>

```ruby
version: "3.7"

services:
  traefik:
    image: traefik
    container_name: traefik
    restart: unless-stopped
    volumes:
      - ./traefik.toml:/etc/traefik/traefik.toml:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    ports:
      - 80:80
      - 443:443
    networks:
      - default
      - traefik


networks:
  traefik:
    name: traefik
```

</details>

Additionally, you'll need a `traefik.toml` configuration file in the same directory:

<details>

<summary>traefik.toml Traefik configuration file</summary>

```
[entryPoints.web]
  address = ":80"

[entryPoints.web.http.redirections.entryPoint]
  to = "websecure"
  scheme = "https"

[entryPoints.websecure]
  address = ":443"

[api]
  debug = true
  dashboard = true

[providers.docker]
  exposedByDefault = false
  network = "traefik"
  defaultRule = "Host(`{{ trimPrefix `/` .Name }}.stage.yourdomain.invalid`)"

[certificatesResolvers.le.acme]
  email = "ssl@yourdomain.invalid"
  storage = "/etc/traefik/acme.json"
  [certificatesResolvers.le.acme.httpChallenge]
    entryPoint = "web"

[http.routers.api]
  entryPoints = ["websecure"]
  rule = "Host(`traefik.stage.yourdomain.invalid`)"
  service = "api@internal"

[http.routers.api.tls]
  certResolver = "le"

[http.middlewares.replace-to-root-path.replacepathregex]
  regex = "^/[a-z]+/[0-9]+/(.*)"
  replacement ="/$$1"
```

</details>

{% hint style="warning" %}
This configuration will expose the Traefik Dashboard publicly without authentication. You should [configure an authentication middleware](https://doc.traefik.io/traefik/middlewares/http/basicauth/) and add it. Also make sure to change the domains to your actual values, also the `email` property for the Let's Encrypt cert resolver.
{% endhint %}

Now, spinning up Traefik with `docker-compose up -d` should provide you with a running Traefik container.

### 2. Add Dockware(s) through `docker-compose`

For each Dockware instance you want to spin up, you'd need to create a service within a `docker-compose.yml`. You could use one for multiple, or one per shop, it's up to you. In this example, we created a file at `~/services/dockware/docker-compose.yml`. Simply make sure to add the correct labels to each instance:

<details>

<summary>docker-compose.yml for two Dockware instances and Traefik</summary>

```ruby
version: "3"
services:
        
    shopware-alpha:
      image: dockware/play:latest
      container_name: shopware-alpha
      networks:
         - traefik
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.shopware-alpha.entrypoints=websecure"
        - "traefik.http.routers.shopware-alpha.rule=Host(`alpha.stage.yourdomain.invalid`)"
        - "traefik.http.routers.shopware-alpha.tls=true"
        - "traefik.http.routers.shopware-alpha.tls.certresolver=le"
        - "traefik.http.services.shopware-alpha.loadbalancer.server.port=80"

    shopware-beta:
      image: dockware/play:latest
      container_name: shopware-beta
      networks:
         - traefik
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.shopware-beta.entrypoints=websecure"
        - "traefik.http.routers.shopware-beta.rule=Host(`beta.stage.yourdomain.invalid`)"
        - "traefik.http.routers.shopware-beta.tls=true"
        - "traefik.http.routers.shopware-beta.tls.certresolver=le"
        - "traefik.http.services.shopware-beta.loadbalancer.server.port=80"
        
networks:
  traefik:
    external:
      name: traefik
```

</details>

Again, make sure to change the domains to something that makes sense in your setup.

Then you should be ready to fire up the two Dockware instances with `docker-compose up -d`. After a short while, `alpha.stage.yourdomain.invalid` and `beta.stage.yourdomain.invalid` should point to two independent Dockware instances.

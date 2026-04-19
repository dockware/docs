# Developing Headless Systems

Headless systems are everywhere nowadays. This could mean an integration of your headless CMS system, or even a full headless E-Commerce shop.

The power in development setups lies within the system architecture, so what containers you have and how they communicate with each other.

The following dockware images can help you:

<table><thead><tr><th width="265.65625">Image</th><th>Why</th></tr></thead><tbody><tr><td>dockware/web</td><td>simple easy image for PHP and Node based applications. You can install your headless frontend, or even your CMS or Shopware shop inside.</td></tr><tr><td>dockware/shopware</td><td>if you just need a "self-managed" simple Shopware version, you can use this as Shopware instance. You can also easily upgrade to a different Shopware version by changing the image tag.<br>In the end, you want to focus on your headless storefront and not on Shopware right ;)</td></tr><tr><td>dockware/shopware-essentials</td><td>This is like the Shopware image, but you have to bring the source code of your own Shopware (version). The benefit is only, that you don't need an additional MySQL and other services for the shop in your setup, because it's all in 1 container.</td></tr><tr><td>dockware/proxy</td><td>A simple NGINX proxy. You probably want to use this, if you want to use those beautiful domains for your systems instead of ports.</td></tr></tbody></table>

## Create Environment

Let's create a folder for our local project and place a **docker-compose.yml** file inside.

Please keep in mind, you can use any Docker images (also different ones than from the list above).\
This sample shows you how to build a headless application with Shopware as backend system.

```yaml
proxy:
  image: dockware/proxy:latest
  container_name: proxy
  ports:
    - 80:80
    - 443:443
  volumes:
    - "../proxy/frontend.conf:/etc/nginx/conf.d/frontend.conf:ro"
    - "../proxy/shopware.conf:/etc/nginx/conf.d/shopware.conf:ro"
    - "../proxy/tcp.conf:/etc/nginx/conf.stream.d/tcp.conf:ro"
  
frontend:
  image: dockware/web:latest
  container_name: frontend
  volumes:
    - "./src:/var/www/html"
  environment:
    - NODE_VERSION=22
    
shopware:
  image: dockware/shopware:6.6.10.2
  container_name: shopware
```

This will launch 3 containers.\
A NGINX proxy that forwards our domains and TCP requests (MySQL clients) to the corresponding containers, a plain environment container with Node 22 for our Vue.js Storefront, as well as a simple pre-installed Shopware 6.6.10.2.

The only thing that is left, before we can start our environment is the 3 NGINX configuration files.

{% tabs %}
{% tab title="frontend.conf" %}
```nginx
server {
    listen        80;
    server_name   shop.my-domain.com;
    return 301    https://$host$uri$is_args$args;
}

server {
    listen        443 ssl;
    server_name   shop.my-domain.com;

    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    location / {
        proxy_pass https://frontend;
   }
}
```
{% endtab %}

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

{% tab title="tcp.conf" %}
```nginx
server {
    listen 3306;
    proxy_pass shopware:3306;
}
```
{% endtab %}
{% endtabs %}

Now just edit your local **/etc/hosts** file and add entries for your domains

```
127.0.0.1     shop.my-domain.com
127.0.0.1.    shopware.my-domain.com
```

{% hint style="success" %}
If you want to use a more dynamic way instead of using /etc/hosts, this blog post could be interesting for you: [https://www.boxblinkracer.com/blog/docker-dnsmasq](https://www.boxblinkracer.com/blog/docker-dnsmasq)
{% endhint %}

And that's it.\
Start your Docker environment and you should be able to access **https://shop.my-domain.com** and **https://shopware.my-domain.com/admin**

```bash
docker-compose up -d
```

{% hint style="info" %}
If your browser shows invalid certificates, just continue (in Chrome just type "thisisunsafe").\
With Shopware, keep in mind, your Storefront is only available if you create a dedicated domain entry in your sales channel - but you actually just need the Administration anyway.
{% endhint %}

## Create Frontend Application

It's time to create your Vue.js Storefront application.\
This tutorial will not cover this in detail.

Just connect into the container and start the installation. The files will be automatically available in your **src** folder on your host system.

```bash
docker exec -it frontend bash
```

The only thing you need to build your application in a headless way, is the domains how to connect to Shopware. There are 2 options for this.

If you need to **access from the client side**, just use the domain **https://shopware.my-domain.com** as you would do to access Shopware directly.

If you need to **access it from the server side**, use the **key** value from the **docker-compose.yml** in combination with **HTTP** (SSL is too much for this tutorial), which is **http://shopware** in our sample.

{% hint style="success" %}
That's it, you now have a working development environment with a simple headless setup.
{% endhint %}

## Where to go from here?

Depending to the complexity of your project, you might require additional things such as new **containers**, **installation scripts** to automatically install dependencies and more.

Instead of Shopware you might need another system like a headless CRM, or even a custom application where you could also use another dockware/web container and maybe an additional MySQL container.

This is not really part of dockware, because it's really bound to your special needs.

The great thing is, it is all **just plain Docker**, so you can basically build and connect everything you need.

Here are a few typical things you usually want in your projects

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>HTTP and SSL</td><td>Use HTTPS instead of HTTP for your projects</td><td><a href="../docker-tutorials/domains-with-https.md">domains-with-https.md</a></td></tr><tr><td>Improve Bind-Mounting</td><td>Optimize performance and structure when it comes to bind-mounting your source code</td><td><a href="../docker-tutorials/correct-bind-mounting.md">correct-bind-mounting.md</a></td></tr><tr><td>Web Image Features</td><td>Explore the features of dockware/web to improve your development envrionment.</td><td><a href="../../dockware-web/features.md">features.md</a></td></tr></tbody></table>

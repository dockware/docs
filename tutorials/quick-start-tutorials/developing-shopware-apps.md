# Developing Shopware Apps

Since a while, Shopware allows the development of apps instead of plugins.

The basic difference between an app and a plugin is, that apps do not directly run within the Shopware shop that has installed it. Only a small layer is really existing in the shop, while the actual source code and logic is running on another server, you're so called backend-server.

This lightweight approach is the way to go when developing for Shopware SaaS, but also a great thing if you want to use another development language instead of PHP. Indeed it's a perfect chance to have a central place of logic and even bring your Shopware SaaS and Shopware On-Premise customers together into 1 single technology.

## Create Environment <a href="#developing-with-dockware" id="developing-with-dockware"></a>

Please make sure to learn the basics about app development from the official [Shopware documentation](https://developer.shopware.com/docs/guides/plugins/apps/app-base-guide).

Developing Apps require an **additional backend system** next to your Shopware shop. \
This is the server that runs your actual code.

Both platforms (Shopware and your backend service) need to be able to communicate with each other, depending on what your app should do. This is easy when developing on live systems with domains that really exist, but could be a bit tricky if your are just working offline within Docker.

So we've created a possible solution for you to easily start with app development and dockware.

Please keep in mind, this is just 1 single solution that could work.

Depending on your setups and wishes, you can of course use other approaches and fully enjoy the power of Docker! In the end, both endpoints just need to be able to talk to each other.

We start by creating 2 containers in our **docker-compose.yml** file. Both of them use a bind-mount for the source code, but you can handle this in your preferred way.

Copy

```yaml
version: "3.0"

services:

    shopware:
      container_name: shopware
      image: dockware/shopware:6.7.2.2
      ports:
        - "80:80"
      networks:
        - web
      volumes:
        - "./.env:/var/www/html/.env"
        - "./YourApp:/var/www/html/custom/apps/YourApp"

    server:
      container_name: server
      image: dockware/web:latest
      ports:
        - "1000:80"
      networks:
        - web
      volumes:
        - "./service:/var/www/html"
      links:
        - shopware:my.app.dev

networks:
  web:
    external: false
```

The 2 containers can communicate with each other, using the key names for the YAML file **shopware** and **server**. So our goal is, to ensure that during the initial handshake and exchange when installing a plugin, the containers use their correct internal Docker hostname when registering.

## **Connect Shopware to Backend Service**

This is really easy. For development purpose, you can just use the Docker host name within the **manifest.xml** file of the Shopware App.

Copy

```
<setup>
   ...
   <registrationUrl>http://server/.....</registrationUrl>
</setup>
```

Don't forget about that dynamic confirmation URL that is sent from your backend service during the setup process. That would also need to use this host!

## **Connect Backend Service to Shopware**

Some use cases might require your backend service to connect to a shop and retrieve data using the Shopware API.

Unfortuantely, Shopware might always send **localhost** as shop URL during our registration, which means, that the backend server would call localhost when trying to communicate with the shop, which obviously results in **communicating with himself**.

So we have to learn Shopware to use another host when registering, while still being available as **http://localhost** for us and our browser.

This might be different, depending on your setup and usage of http://localhost when developing.

Changing the shop URL can be done by adjusting the **.env** file of Shopware. Just use any domain name that you want. It does not need to even exist. Not even in your /etc/hosts file.

Copy

```
...
APP_URL=http://my.app.dev
...
```

Now Shopware would automatically send this domain to the backend service when registering the shop. The only problem left is, that our backend service does not know whom to contact with that domain.

The solution is quite simple.

We use a **link** and an **alias** in the docker-compose.yml where we just learn the container to call our Shopware host when using this domain. And therefore the backend service will automatically connect to our our Shopware container when trying to reach it.

Copy

```
links:
   - shopware:my.app.dev
```

Congratulations!

You have a Shopware app environment where your backend service just gets a dynamic shop URL and data, and can connect to it without any additional troubles.

Now it's up to you, to create the purpose and logic of your software!

## Logging <a href="#logging" id="logging"></a>

Logging is always important, but even more if you have decentralized systems such as with developing app systems for Shopware.

We recommend using systems like ELK (Elastic Logstash Kibana) for such setups. But if you need a quick start and easy solution, then we have a guide on that using the built-in Pimp my Log of dockware. [Read more here](https://docs.dockware.io/features/pimp-my-log).

## Video <a href="#video" id="video"></a>

Need more explanation? Just watch the video where we demonstrate the setup, connection as well as our example application in more detail.

{% embed url="https://www.youtube.com/watch?v=4vZ8V_IIjck" %}

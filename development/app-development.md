# App Development

Since a while, Shopware allows the development of apps instead of plugins.

The basic difference between an app and a plugin is, that apps do not directly run within the Shopware shop that has installed it. Only a small layer is really existing in the shop, while the actual source code and logic is running on another server, you're so called backend-server.

This lightweight approach is the way to go when developing for Shopware SaaS, but also a great thing if you want to use another development language instead of PHP. Indeed it's a perfect chance to have a central place of logic and even bring your Shopware SaaS and Shopware On-Premise customers together into 1 single technology.



### Developing with dockware

Developing Apps require an additional server. This is the server that runs your actual code.

Thanks to Docker and it's power to launch additional containers, it's an ease to develop this type of projects.&#x20;

Both platforms (Shopware and your backend service) need to be able to communicate with each other, depending on what your app should do. This is easy when developing on live systems with domains that really exist, but could be a bit tricky if your are just working offline within Docker.

So we've created a possible solution for you to easily start with app development and dockware.

{% hint style="info" %}
Please keep in mind, this is just 1 single solution that could work.

Depending on your setups and wishes, you can of course use other approaches and fully enjoy the power of Docker! In the end, both endpoints just need to be able to talk to each other.
{% endhint %}

We start by creating 2 containers in our **docker-compose.yml** file. Both of them use a bind-mount for the source code, but you can handle this in your preferred way.

```yaml
version: "3.0"

services:

    shopware:
      container_name: shopware
      image: dockware/dev:6.4.6.1
      ports:
        - "80:80"
      networks:
        - web
      volumes:
        - "./.env:/var/www/html/.env"
        - "./YourApp:/var/www/html/custom/apps/YourApp"

    server:
      container_name: server
      image: dockware/flex:latest
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

#### Connection Shopware to Backend Service

This is really easy. For development purpose, you can just use the Docker host name within the **manifest.xml** file of the Shopware App.

```xml
<setup>
   ...
   <registrationUrl>http://server/.....</registrationUrl>
</setup>
```

{% hint style="warning" %}
Don't forget about that dynamic confirmation URL that is sent from your backend service during the setup process. That would also need to use this host!
{% endhint %}

#### Connection Backend Service to Shopware

Some use cases might require your backend service to connect to a shop and retrieve data using the Shopware API.

Unfortuantely, Shopware might always send **localhost** as shop URL during our registration, which means, that the backend server would call localhost when trying to communicate with the shop, which obviously results in **communicating with himself**.&#x20;

So we have to learn Shopware to use another host when registering, while still being available as **http://localhost** for us and our browser.

{% hint style="warning" %}
This might be different, depending on your setup and usage of http://localhost when developing.
{% endhint %}



Changing the shop URL can be done by adjusting the **.env** file of Shopware. Just use any domain name that you want. It does not need to even exist. Not even in your /etc/hosts file.

```ini
...
APP_URL=http://my.app.dev
...
```

Now Shopware would automatically send this domain to the backend service when registering the shop. The only problem left is, that our backend service does not know whom to contact with that domain.&#x20;

The solution is quite simple.&#x20;

We use a **link** and an **alias** in the docker-compose.yml where we just learn the container to call our Shopware host when using this domain. And therefore the backend service will automatically connect to our our Shopware container when trying to reach it.

```yaml
links:
   - shopware:my.app.dev
```

{% hint style="success" %}
Congratulations!

You have a Shopware app environment where your backend service just gets a dynamic shop URL and data, and can connect to it without any additional troubles.

Now it's up to you, to create the purpose and logic of your software!
{% endhint %}

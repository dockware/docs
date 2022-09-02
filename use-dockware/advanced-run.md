# Advanced Run

Let's talk about an advanced setup for running Shopware 6.

The easy first run is perfect to immediately start Shopware, but does not persist any data or allows you to reuse that Shopware 6 instance again after restarting your host.

Use this content and place it in a file called **docker-compose.yml**.

It will use internal Docker volumes to persist your database and everything that is stored within your DocRoot, such as "media" files, "download" files and more.

```yaml
version: "3"

services:
        
    shopware:
      image: dockware/play:latest
      container_name: shopware
      ports:
         - "80:80"
      networks:
         - web
        
networks:
  web:
    external: false
```

Now start dockware with this command from the terminal with the directory that contains your yaml file as working directory:

```yaml
docker compose up -d
```

The option "-d" means "detached" and makes sure your container isn't quit when you close your terminal.\
If you still want to see the output of dockware, use this command.ON HOST

```
docker logs -f shopware
```

{% hint style="info" %}
You can also find some other and deeper examples in our [dockware examples](https://github.com/dockware/examples) Git Repo
{% endhint %}

{% hint style="success" %}
If you are looking for passwords and default credentials of Shopware and dockware, please take a look at this page: [Default Credentials](default-credentials.md)
{% endhint %}

###

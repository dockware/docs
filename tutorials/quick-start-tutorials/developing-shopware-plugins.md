# Developing Shopware Plugins

The Shopware images of dockware are a perfect fit if you want to develop Shopware plugins.

\
Every image comes with an **installed** and **prepared Shopware**, so you can **immediately start coding**. Depending on your environment strategy, you can even **switch** to a different Shopware version by just changing one single version number. Sounds good? Let's discover more.

## Environment Strategy

The first thing you want to clarify is, what **type of infrastructure** your project needs.

You can always create custom installations of Shopware by using the plain **dockware/we**b image, additional database containes or more.&#x20;

But we usually **recommend** using the **dockware/shopware** image and just bind mount your plugin to the demo installation of Shopware. This allows you to easily switch Shopware versions without a lot of hazzle.

The next thing to decide, is, if you need the full source code of Shopware **locally in your IDE** to use code completion and more.

Our sample in this tutorial, will use exactly this approach.\
We will only mount our plugin, but also prepare the source code of Shopware locally for a better developer experience.

## Create Environment

We start by creating a **docker-compose.yml** file.

This will typically only contain 1 single container. However, depending on your needs, you can always add more, such as ELK stacks for logging and other useful and required services for your developer experience.

```yaml
shop:
    image: dockware/shopware:6.7.2.2
    container_name: shop
    volumes:
      # ----------------------------------------------------------------------------------------------------------------------------------
      # PLUGINS
      # our plugin GIT repository is in a sub folder custom/plugins
      - "./custom/plugins/MyPlugin:/var/www/html/custom/plugins/MyPlugin"
      # ----------------------------------------------------------------------------------------------------------------------------------
      # exclude some folders for better performance
      # this uses anonymous volumes that leads to container/host decoupling for these folders
      # therefore less bind-mount -> better performance
      - "/var/www/html/custom/plugins/MyPlugin/.git/"
      - "plugin_vendor:/var/www/html/custom/plugins/MyPlugin/vendor/"
      - "plugin_node_admin:/var/www/html/custom/plugins/MyPlugin/src/Resources/app/administration/node_modules/"
      - "plugin_node_storefront:/var/www/html/custom/plugins/MyPlugin/src/Resources/app/storefront/node_modules/"
    ports: 
      - "80:80"
      - "443:443"
      - "3306:3306"
    environment:
      - XDEBUG_ENABLED=1
```

And that's it.\
Start your Docker environment and you should be able to access **http(s)://localhost** to access your Shopware.

```bash
docker-compose up -d
```

{% hint style="info" %}
If you need to use domains instead of localhost, please see this page [domains-with-https.md](../docker-tutorials/domains-with-https.md "mention")
{% endhint %}

{% hint style="info" %}
If your browser shows invalid certificates, just continue (in Chrome just type "thisisunsafe").\
With Shopware, keep in mind, your Storefront is only available if you create a dedicated domain entry in your sales channel - but you actually just need the Administration anyway.
{% endhint %}

## Prepare Local Shopware

When you either **initially** start your project, or after **switching** to a different Shopware version by changing the docker-compose.yml file, you might want to download the source code to use **code completion in your IDE**.

Why do we need this? Because we have only mounted our plugin for a better performance but also decoupling of the used Shopware version.

So all we need now, is a **small script** that just downloads the files from the container to your host.\
This is not needed all the time, just initially or after changing Shopware versions.

Here is a sample based on a **makefile** with the command make `download-src`. It first cleans our local vendor folder of Shopware, and then just downloads everything. Feel free to improve it for you.

```makefile
download-src: 
    rm -rf ./vendor/* || true
    docker cp shop:/var/www/html/. ./
```

{% hint style="success" %}
And that's it, you now have everything locally with a wonderful developer experience!
{% endhint %}

## Where to go from here?

Depending to the complexity of your project, you might require additional things such as new **containers**, **installation scripts** to automatically install dependencies and more.

This is not really part of dockware, because it's really bound to your special needs.\
The great thing is, it is all **just plain Docker**, so you can basically build and connect everything you need.

Here are a few typical things you usually want in your projects

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>HTTP and SSL</td><td>Use HTTPS instead of HTTP for your projects</td><td><a href="../docker-tutorials/domains-with-https.md">domains-with-https.md</a></td></tr><tr><td>Improve Bind-Mounting</td><td>Optimize performance and structure when it comes to bind-mounting your source code</td><td><a href="../docker-tutorials/correct-bind-mounting.md">correct-bind-mounting.md</a></td></tr><tr><td>Shopware Image Features</td><td>Explore the features of dockware/shopware to improve your development envrionment.</td><td><a href="../../dockware-shopware/features.md">features.md</a></td></tr></tbody></table>

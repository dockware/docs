# Bind-Mounting

Since the Docker engine is getting better, the performance losses especially on MACs are not as big as they used to be when using bind-mounting in projects such as Symfony or Shopware.

Some of you know, that we like the SFTP way, as it's the best controllable and platform independent way to handle the project and file permissions.&#x20;

**But it's not the only approach of dockware and us! It's a fallback that always works!**

{% hint style="warning" %}
Bind-Mounting is a plain Docker feature and has nothing to do with dockware itself.\
Thus, all related things, including issues such as file permission problems do not have to do anything with dockware. Please keep that in mind when searching for answers to problems.
{% endhint %}

{% hint style="info" %}
**WINDOWS Users:**\
Please see also this page for now, until we had time to integrate it in this page.\
Thank you for your understanding:\
[https://github.com/dockware/docs/issues/3](https://github.com/dockware/docs/issues/3)
{% endhint %}

## New Project

If you are not a plugin developer, but have a full shop instead, please make sure to read our guide about [creating a new project](create-a-new-project.md).

This is required, because starting a new project immediately with bind-mount would lead to an empty folder, and thus no Shopware.\


So **with bind-mounting, you first need your source code on your host machine.**



## MAC

If you are working on a MAC, then bind-mounting works really good.\
You only need to exclude a few folders from your container, and it works blazing fast.

Below are 2 use cases with samples of folders that should usually be excluded.\
Feel free to improve these for your custom projects.&#x20;

> We recommend excluding every large folder that you don't need locally \
> (logs, caches, node\_modules, ...)

The excluded folders will be based on **anonymous volumes**.\
It's like a a persistent volume...only without a name and known destination.&#x20;

This means, that if you have a _node\_modules_ folder as an anonymous volume, it only exists in Docker, but not on your host, which is the solution to speed up things. But this also means that Docker is the **owner** of those folders, which brings a small disadvantage.

If you want to delete those folders in your scripts (like the vendor), then you get an error "Device is busy". The solution is to simply delete the contents of that folder, instead of the folder itself.&#x20;

```bash
rm -rf ...../vendor/*
```

To continue using all your scripts and commands as usual, you have to set permissions on these folders after starting your container. **You can set these individually per folder, or just use this command and all is good:**

```bash
docker exec -it shop bash -c 'sudo chown www-data:www-data /var/www/html -R"
```

{% hint style="success" %}
That's it! \
You now have a bind-mounted Shopware on a MAC with a good performance.
{% endhint %}

### Plugin Developer

{% code title="docker-compose.yml" %}
```yaml
shop:
  image: dockware/dev:6.4.9.0
  container_name: my_shop
  volumes:
    - "./src/custom/plugins/MyPlugin:/var/www/html/custom/plugins/MyPlugin"
    # exclude by using anonymous volumes
    - "/var/www/html/custom/plugins/MyPlugin/.git/"
    - "/var/www/html/custom/plugins/MyPlugin/vendor/"
    - "/var/www/html/custom/plugins/MyPlugin/src/Resources/app/administration/node_modules/"
    - "/var/www/html/custom/plugins/MyPlugin/src/Resources/app/storefront/node_modules/"
    - "/var/www/html/custom/plugins/MyPlugin/tests/Cypress/"
   ...rest of docker-compose...
```
{% endcode %}

### Shop Developer

{% code title="docker-compose.yml" %}
```yaml
shop:
  image: dockware/essentials:latest
  container_name: my_shop
  volumes:
    - "./src:/var/www/html/"
    # ...excluding shopware default directories
    - "/var/www/html/.git/"
    - "/var/www/html/public/build"
    - "/var/www/html/var/cache"
    - "/var/www/html/vendor"
    # ...additional project specific excludes...
    - "/var/www/html/custom/plugins/MyPlugin/src/Resources/app/administration/node_modules/"
    - "/var/www/html/custom/plugins/MyPlugin/src/Resources/app/storefront/node_modules/"
    - "/var/www/html/custom/plugins/MyPlugin/tests/Cypress/"
   ...rest of docker-compose...
```
{% endcode %}

## Linux

Bind-Mounting on Linux works great!\
You can simply mount the whole DocRoot without any performance loss.

However, if you are a plugin developer, we would still recommend only mounting your custom plugin. This allows you to easily switch the Shopware version around your plugin.

Here are 2 samples for plugin developers and developers who are in charge of full shops.

### Plugin Developer

```bash
shop:
  image: dockware/dev:6.4.9.0
  container_name: my_shop
  volumes:
    - "./src/custom/plugins/MyPlugin:/var/www/html/custom/plugins/MyPlugin"
   ...rest of docker-compose...
```

### Shop Developer

{% code title="docker-compose.yml" %}
```yaml
shop:
  image: dockware/essentials:latest
  container_name: my_shop
  volumes:
    - "./src:/var/www/html/"
   ...rest of docker-compose...
```
{% endcode %}

Linux has some great options for permissions.\
If you face any troubles while developing, you can do the following:

Make sure that both your host (and users) as well as the Docker container work with the same permission group. We recommend **ID** **33**, which is also known as "**www-data**".

Then simply change the permissions of your source folder

```bash
# change basic permissions (you might need sudo)
chgrp -R 33 ./src
# write permissions for cache/log folder required
chmod a+w ./src/var/*
```

Another approach would be to run this command in your container after starting it.\
It should fix all permissions if something is broken

```bash
sudo chown www-data:www-data /var/www/html -R
```

There would also be an easy make command to fix permissions in /var/www.



{% hint style="info" %}
Please keep in mind, this guide is meant for development.\
When it comes to a hosted installation on a Linux server, we have more instructions about strategies and permissions on [this page.](online-servers.md)
{% endhint %}



## Important Notices

{% hint style="danger" %}
Keep in mind, when you upload with SFTP while having an active bind-mount, that will remove thecontent of the files! Do not mix both ways!
{% endhint %}

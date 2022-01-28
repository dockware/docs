# Bind-Mounting

Since the Docker engine is getting better the performance losses especially on MACs are not as big as they used to be when using bind-mounting in projects such as Symfony or Shopware.

Some of you know, that we like the SFTP way, as it's the best controllable and platform independent way to handle the project and file permissions.

But we get often asked on how to work with dockware and bind-mounting, which is indeed possible.

{% hint style="warning" %}
Bind-Mounting is a plain Docker feature and has nothing to do with dockware itself.\
Thus, all related things, including issues such as file permission problems do not have to do anything with dockware. Please keep that in mind when searching for answers to problems.
{% endhint %}

Let's get started with our bind-mount setup.

{% hint style="info" %}
**WINDOWS Users:**\
Please see also this page for now, until we had time to integrate it in this page.\
Thank you for your understanding:\
\
[https://github.com/dockware/docs/issues/3](https://github.com/dockware/docs/issues/3)
{% endhint %}

## 1. Preparing a fresh and clean project

An easy starting point for that is to use our "dev" example on Github. [https://github.com/dockware/examples/tree/master/bind-mount](https://github.com/dockware/examples/tree/master/bind-mount)

1. Create a folder like `src` on your host (maybe in the same directory as your docker-compose...)

## 2. Initial Start with Dockware files (no bind-mount)

{% hint style="info" %}
This step has only to be done once per project!

And only if you use **dockware:dev** which provides already an installed shopware version. If you want to use your own project/installation you can go to step 3.
{% endhint %}

{% hint style="warning" %}
**For Linux users only! (To be executed on your HOST, not docker)**

1. Make sure your host user is in the `docker` and `www-data` groups (check with `groups` command)
2. Restart your pc to make sure the permissions get granted
{% endhint %}

This steps only required if you want to use the shopware version provided by your **dockware:dev** container. Not If you want to use your own project/installation.

1. Make sure your host `src` directory is empty 
2. Make sure the `volumes` are **commented out** in the `docker-compose.yml` 
3. Start the container with `docker-compose up -d`
4. Initially copy the dockware files to your host with: `docker cp shopware:/var/www/html/. ./src`

You should now have a "src" folder in your directory.\
That folder contains the whole Shopware files, including .env and whatever is located in the DocRoot of Apache.

Congratulations, you now have a local Shopware version to work with.

## 3. Set Permissions

{% hint style="warning" %}
**For Linux users only! (To be executed on your HOST, not docker)**

1. Set permissions (`chgrp -R www-data src` ) - possible that you need sudo for this
2. Set write permissions for cache/log (`chmod a+w src/var/*` )
{% endhint %}

## 4. Enable Bind-Mounting

Now you have to modify your `docker-compose.yml` and enable the bind-mounting.

You can find a full docker-compose example here [https://github.com/dockware/examples/tree/master/bind-mount](https://github.com/dockware/examples/tree/master/bind-mount)

{% code title="docker-compose.yml" %}
```
...

volumes:
      - "./src:/var/www/html"

....
```
{% endcode %}

## 5. Restart the container

Now restart your container and bind-mounting including all your Shopware files is ready to use.\
You can now change files on your host, and it will be immediately shown without uploading it using SFTP

{% hint style="danger" %}
Keep in mind, when you upload with SFTP while having an active bind-mount, that will remove the files! Do not mix both ways!
{% endhint %}

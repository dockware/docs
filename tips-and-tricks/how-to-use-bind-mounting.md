# How to use Bind-Mounting

Since the Docker engine is getting better the performance losses especially on Macs are not as big as they used to be when using bind-mounting in projects such as Symfony or Shopware.  
  
Some of you know, that we like the SFTP way, as it's the best controllable and platform independent way to handle the project and file permissions.

But we get often asked on how to work with dockware and bind-mounting, which is indeed possible.

{% hint style="warning" %}
Bind-Mounting is a plain Docker feature has nothing to do with dockware itself.  
Thus all related things, including issues such as file permission problems do not have to do anything with dockware. Please keep that in mind when searching for answers to problems.
{% endhint %}

Let's get started with our bind-mount setup.



### 1. Prepare a clean project 

An easy starting point for that is to use our "dev" example on Github. [https://github.com/dockware/examples/tree/master/basic-dev-setup](https://github.com/dockware/examples/tree/master/basic-dev-setup)

### 2. First Start with Dockware files \(no bind-mount\)

{% hint style="info" %}
This step has only to be done once!
{% endhint %}

{% hint style="warning" %}
**For Linux users only!**

1. Make sure your host user is in the `docker` and `www-data` groups \(check with `groups` command\)
{% endhint %}

1. Create a folder like `src` on your host \(maybe in the same directory as your compose...\)
2. Make sure your host `src` directory is empty
3. Make sure the `volumes` are **commented out** in the `docker-compose.yml`
4. Start the container with `docker-compose up -d`
5. Initially copy the dockware files to your host with: `docker cp shop:/var/www/html/. ./src`
6. Move the content around \(`cd src && mv html/* . && mv html/.env . && rmdir html && cd ..` \)

{% hint style="warning" %}
**For Linux users only!**

1. Set permissions \(`chgrp -R www-data src` \) - possible that you need sudo for this
2. Set write permissions for cache/log \(`chmod a+w src/var/*` \)
{% endhint %}

### 4. Enable Bind-Mounting

Now you have to modify your `docker-compose.yml` and enable the bind-mounting.

{% code title="docker-compose.yml" %}
```text
...

volumes:
      - "./src:/var/www/html"
      
....
```
{% endcode %}

### 4. Restart the container

Now restart your container and bind-mounting including all your Shopware files is ready to use.  
You can now change files on your host, and it will be immediately shown without uploading it using SFTP

{% hint style="danger" %}
Keep in mind, when you upload with SFTP while having an active bind-mount, that will remove the files! Do not mix both ways!
{% endhint %}



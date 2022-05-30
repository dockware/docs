---
description: Online Servers with Dockware Containers
---

# Online Servers

Dockware can also be used on online servers and not only on local developer machines.\
A perfect use case would be a test environment for your QA department!

{% hint style="warning" %}
Please note, we do **not recommend** or encourage you to use dockware for production!\
In theory it's possible, but it requires way more knowledge in Docker, hosting and also backup strategies! So please don't do it (unless you may know what you are doing).
{% endhint %}

## Hosting Strategy

When running dockware, or any other Docker container on an online server, you need to think about the strategy you are going to use to rollout new updates for your application.

Most of these scenarios depend on how you use your containers - either **with** bind-mounting or **without** bind mounting.

Docker containers are used as virtual servers on your hosting system.\
It's indeed possible to expose access to a container and deploy your application directly to it, exactly like you would do with a "real" server. So a pretty good and stable thing.\
But Docker containers might also run into problems - so you need to think about the case when you need to restart them. You don't want to lose any data in the end!

This could mean, that it might be a better solution, to keep the deployment to the host system and use Docker only for running and providing your application.

All these different scenarios are listed below, including pros/cons, as well as some instructions to get you started.

* **Scenario 1**: No Bind-Mount / Deployment to Container
* **Scenario 2**: No Bind-Mount / Deployment to Host
* **Scenario 3**: Bind-Mount / Deployment to Host **(Recommended)**&#x20;
* **Scenario 4**: Dockerized Application&#x20;

{% hint style="info" %}
We recommend Linux systems when using Docker for your online server....just saying ;)
{% endhint %}

## Reverse Proxy

Besides the hosting strategy, we suggest that you use a **reverse** **proxy** to manage the exposed ports.&#x20;

It can be very dangerous if you expose port in your containers directly!\
&#x20;\
A compose setup can have lots of containers, and it can easily happen that a container is suddenly available public with an accidentally exposed port! \
We recommend that you have your **ports only exposed through your proxy** where you can manage what should be available and what must not be exposed!

{% hint style="success" %}
We've created [dockware/proxy](https://hub.docker.com/r/dockware/proxy) as easy to use Docker image.\
It's based on NGINX and comes with some nice setups and features.
{% endhint %}

## Hosting Strategies

### Scenario 1: No Bind-Mount / Deployment to Container <a href="#scenario-1" id="scenario-1"></a>

| PROs                                                                   | CONs                                                                                                                                                                                                                            |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| No Permission Problems                                                 | <p>Files are only in your container or Docker volume. <br>Once restarted or pruned, all files might be gone</p>                                                                                                                 |
| Grant externals access to only your container but not the host system. | <p>One might easily forget about security concepts when using Docker images that are meant for development.</p><p>Default strategies on the host system (users, port restrictions, SSH keys) might just feel better, right?</p> |

This scenario is a good way if you need temporary access to your container (easy and fast), or if you need access for external people, while trying to avoid that they can acess your host system.

Dockware allows you to set **custom SSH/SFTP users** for your container.\
Configure one with a good password strength, open **port 22** of your container, and you should be able to easily connect into your container from a remote machine. \
When exposing your port with "22:22", it could be that your host system already uses that port.\
In that case, you might want to switch to something else like "1022:22" (or any other port). \
Changing the SSH port to something else isn't a bad idea anyway (Security through obscurity).

In fact, the users, who will gain access from you, might not even notice that they work in a container and not on the host system.

The problem with this approach however is, that you rely on the built-in (open source) dockware project that everyone has access. We tried to build the security concept as good as possible, but still, people could find some weaker spots. And also things like "breakouts" from a Docker container would be possible...at least in theory.

{% hint style="success" %}
You should now have a running dockware container, that allows you to directly access it remotely using SSH/SFTP.
{% endhint %}



### Scenario 2: No Bind-Mount / Deployment to Host <a href="#scenario-2" id="scenario-2"></a>

| PROs                                                                                                       | CONs                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| No Permission Problems                                                                                     | You alway s need to make sure to upload your files into the container after a deployment.                                                                                                                                                                                                               |
| Files are on your host and can be easily reuploaded if you have to restart or prune your Docker system.    | <p>When using "docker cp", you do not delete outdated files in your container. You need to either remove the source folders before using "docker cp" or use something like "rsync" into your local container. </p><p>This means keeping both directory in sync might not be easy for some projects.</p> |
| You can use the host system security for user access and deployments (users, port restrictions, SSH keys). |                                                                                                                                                                                                                                                                                                         |

A deployment with this scenario is split into 2 parts:

* Deployment to Host
* Upload to Docker

You can **deploy your files** to any of your folder on your **host system**.\
There should not be any big file problems if you setup your server without any special configurations.\
Once your files have been deployed, you need to **start a script** (or just execute the commands), to **upload your changes into your Docker container**.

Please note, that files, that would have been removed, will not be removed using the "docker cp" command. This means you need to **manually remove files** or folders that could have changed, or use something like **Rsync** from your host machine into Docker.

After our upload with "docker cp", we need to **adjust the file permissions**.\
Our Apache user "www-data" needs correct access to all these files. These permissions are unfortunately broken after using "docker cp", so we just fix them with a simple command.

Here you will find 2 options with either a full cleanup, or a cleanup of a specific folder before uploading your changes. Please adjust them for your needs.

{% tabs %}
{% tab title="Full Cleanup" %}
```bash
# remove all old files just to be sure
# this is a complete and full cleanup of the previous data
docker exec shop bash -c 'cd /var/www/html && rm -rf *.* && rm -rf *'

# upload new files and fix permissions
docker cp ./src/. shop:/var/www/html
docker exec shop bash -c 'sudo chown www-data:www-data -R /var/www'
```
{% endtab %}

{% tab title="Partial Cleanup" %}
```bash
# remove only the source folder and keep other
# things like "media", ...
docker exec shop bash -c 'cd /var/www/html && rm -rf src'

# upload new files and fix permissions
docker cp ./src/. shop:/var/www/html
docker exec shop bash -c 'sudo chown www-data:www-data -R /var/www'
```
{% endtab %}
{% endtabs %}

###

### Scenario 3: Bind-Mount / Deployment to Host (Recommended) <a href="#scenario-3" id="scenario-3"></a>

| PROs                                                                                              | CONs                                                                                               |
| ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Easy deployment to your host system -> it's immediately synchronized in your container.           | Permission problems can occur if you don't configure both, your Docker and your Host system.       |
| Files are on your host system and are usually not lost when your Docker system is being restarted | Bind-Mounting might impact performance (depends on host operating system / Linux is great though). |
| Use host security concepts for users, port restrictions, SSH keys, ....feels good right?          |                                                                                                    |

For this scenario, we have to configure a few things before we start.

{% hint style="warning" %}
The most important thing for this setup is, that both, our **user on the host system**, as well as the **Apache user in dockware** belong to the **same user group "www-data"**. \
This is the foundation for everything.
{% endhint %}

Now we need to configure, that all our files and folders in our bind-mounted folder can be **accessed and written** by any user who **belongs to this group**.

This means, that both, our host users, and our Docker user, can modify all files without permission problems. So we can easily deploy files to our host server using one of our host users, and also dockware can use those files with all its required permissions.

To configure this scenario, please follow these 3 steps:

#### 1. Host User

Create a user on your host system, setup SSH keys or whatever you want to use for deployments, and finally add him to the "www-data" group.

```bash
usermod -a -G www-data myUser
```

#### 2. Default Permissions

We configure our "my-src-folder" to grant full file access to the user who created it, as well as the group he belongs to. The rest ("others") will not get write access.

```bash
setfacl -R -d -m u::rwx -m g::rwx -m o::r-x /.../.../my-src-folder
```

#### 3. Update Existing Files

If your directory is not empty, you might want to adjust the existing files. \
Just set the owner to "www-data" and the group permissions to 775.

```bash
chown www-data:www-data -R /.../.../my-src-folder
chmod 775 -R /.../.../my-src-folder
```

#### 4. Execute Commands in Container

Please keep in mind, to use the **correct group** of your user, when executing commands in your container. Just use the www-data user/group, and every command you run, will use the same group, and thus everyone has the same access to your files.

```
docker exec -u www-data:www-data myContainer bash -c '...my...command'
```

{% hint style="success" %}
That's it!\
If you now start dockware, it should all work. \
And if you deploy any files to the host system using your host user, there should not be any permission problems, and the changes should be immediately available in your Docker container.
{% endhint %}

###

### Scenario 4: Dockerized Application <a href="#scenario-4" id="scenario-4"></a>

| PROs                                                                                                | CONs                                                                                          |
| --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| Easy deployment by pulling docker image updates and restarting it                                   | No easy and simple rollout of your latest source changes                                      |
| Always stable (or at least the version you've published)                                            | Requires Docker Image "Tag Management" and might be better if you have real version releases. |
| Docker layers allow you to see changes (by you or intruders) compared to your original Docker image |                                                                                               |

This scenario gives you the best option to handle releases and also rollbacks of those deployments.\
When "dockerizing" your application, you basically embed your application into a Docker image and freeze it with the current release of your application. You usually tag your Docker image with anything that relates to your application version, such as "company/myapplication:1.5.2".

When rolling out a new version of your image, you just build a new image version, tag it, and then simply pull your new Docker image on your server and start it, instead of the previous one. That's it!&#x20;

Dockware is also shipped in that way to give you a fast and reliable plug'n'play Docker image of any Shopware version.

If you want to dockerize your own shop or application with dockware or other Docker images, you might want to dig deeper into building Docker images and manage their releases and tags.

{% hint style="info" %}
Please understand, that we cannot explain this here. Feel free to see the official Docker documentation and guides.
{% endhint %}

## Security Concerns

Please note, that hosting a Docker container on an online server requires some thoughts about security concepts. Thus we do not recommend using it for production unless you really know what you are doing.

If you have a staging or test system, we recommend to secure access to your server as good as possible!

This is a list of things that you may want to think about, before hosting your dockware container on an online server.

* [ ] Is the **access to your host system** secured as good as possible?\
  _(port restrictions, users with pub key authentication, firewall rules, ...)_\

* [ ] Did you **change** the easy (_developer_) **credentials** of your dockware systems?\
  _(SSH/SFTP users, MySQL users, Shopware admin ...)_\

* [ ] Is the **access** to your **Adminer** and **PimpMyLog** from dockware restricted or turned off?\
  _We recommend using a plain dockware image if possible (dockware/flex) that does not come with any Adminer or anything else. These additional services (MySQL, Adminer, ...) should be hosted on a separate container if needed and possible, based on your architecture and knowledge._\
  __
* [ ] Did you use a **reverse proxy** to secure and manage traffic and routings to your containers?\
  _Try to avoid direct port exposing in containers. Docker will automatically expose whatever you tell it to do and simply override your firewall by appending additional entries in your iptables!_\
  _That means, if you have a firewall restriction of port "x" but expose that one in your container - then it will NOT BE RESTRICTED!_

{% hint style="info" %}
Did we forget anything?!\
**Help to improve** our dockware universe with your knowledge and drop us a line in the Shopware Slack channel!
{% endhint %}

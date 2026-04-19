# Exploring Shopware

You can explore any Shopware 6, and even supported Shopware 5 versions, even if you are not a technical person. Just follow these easy steps to get started.

This page does also explain you a bit more about the technology and troubleshooting.



{% stepper %}
{% step %}
### Install Docker

Install Docker on your system. Please see this page: [..](../../ "mention")
{% endstep %}

{% step %}
### Start Dockware Image

Now that you have Docker installed and running, it's time to start a new **container**.\
Containers are **running instances** of an **image** in Docker.

A single image contains multiple versions, which is called **tags**. You need the image **dockware/shopware**. The tag is the Shopware version.\
\
Enter the following line in your **terminal** to start a new Shopware 6.6.10.2, that exposes port 80. **Port 80** is the HTTP port. Because of this, you can then open Shopware with **http://localhost** in your browser.

```bash
docker run -p 80:80 dockware/shopware:6.7.2.2
```

Image versions are first downloaded to your local computer before they are started. This might take a bit the first time, but once downloaded, it's really super fast the next time.

{% hint style="success" %}
Congratulations, you now have a Shopware running. Please see this page for default credentials, in case you need it: [default-credentials.md](../../dockware-shopware/default-credentials.md "mention")
{% endhint %}

{% hint style="warning" %}
Please note, that for installing older Shopware versions (< 6.6.x) you need to use our old images. This means if you were prompted that the image was not found, you try using either a non-existing image (like 6.5.9.13). In this case, please use dockware/**play**:x.x.x.x.
{% endhint %}
{% endstep %}

{% step %}
### Delete Dockware Image

If you want to delete the docker container, be it for a fresh installation or just cleaning up your machine, there is another **easy command** to do so.\
Because we have started the Docker container in an synchronous way, we have to **cancel your terminal command**, usually by using something like `CTRL+C`, to be able to enter a new command. Or we can just add a new terminanl window or tab.

Use these commands to easily and manually **delete** (any) running containers.\
Keep in mind, if you ever get something like "**port already used**" when starting a container, it also means an old container is still blocking the port. Just delete all containers.

```bash
// show all containers
docker ps -a
// now pick the ID of the container you want to remove
docker rm -f (id)
```

{% hint style="success" %}
That's it, your container is now successfully removed.
{% endhint %}


{% endstep %}
{% endstepper %}

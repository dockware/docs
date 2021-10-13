# Custom Images

There can be times when you want to modify your dockware image without really changing the image itself. This can happen if you just need to install an additional package, or maybe adjust a few file permissions or anything else during the launch of the container.

This is possible with 2 different approaches:

* Inject Script in Entrypoint
* Custom Build from Dockware 

## Inject Script in Entrypoint

The latest versions of dockware images allow you to inject your custom script in the boot entrypoint of the dockware image.

It's possible to either get yourself into the beginning of the entrypoint, or in the end of the script.

For this, you either need to mount or create a file `/var/www/boot_start.sh` or `/var/www/boot_end.sh` before starting your image. If either of these files exist, they will be executed in the beginning or at the end of the dockware entrypoint script.

Here's an example of a custom boot_start.sh file:

{% code title="boot_start.sh" %}
```bash
# change our user to something else than UID 33. 
# this also proves we can use sudo
sudo sed -i 's/dockware:x:33:33:/dockware:x:7788:33:/g' /etc/passwd
```
{% endcode %}

Now mount the file into your container:

{% code title="docker-compose.yml" %}
```yaml
  shop:
    image: dockware/flex:latest
    volumes:
      - "./boot_start.sh:/var/www/boot_start.sh"
```
{% endcode %}

If you now start your Docker container, it should execute your custom script before it will continue with the original entrypoint of dockware.

If you want to see what it does, just use the `docker logs` command.

## Custom Build from Dockware

The approach above will execute your script every time the container is being started.\
But what if you want to adjust different packages - do you really want to install them over and over again?

Probably not ;)

In this case, you might want to use the default Docker feature to build a new image on the fly.\
With this, your custom code is executed only once, which leads to a new local image that will be created. When you start your container a second time, it will reuse the built image and immediately start the container.

This is perfect, if you only need a few adjustments of dockware (maybe just for 1 single customer project).

To start, please adjust your `docker-compose.yml` to use **build** instead of an **image** and point to a local directory that we will create next.

{% code title="docker-compose.yml" %}
```yaml
shop:
  build: ./custom
  ....
```
{% endcode %}

Now create a new folder `custom` in the same directory and place a file `Dockerfile` in it.\
This is a plain Dockerfile and can do everything usual Dockerfiles can do.

In this example, we simply inherit from the latest dockware "**flex**" image and install a new package to your image:

{% code title="Dockerfile" %}
```bash
FROM dockware/flex:latest

sudo apt-get update
sudo apt-get install -y ....my package....
```
{% endcode %}

{% hint style="success" %}
That's it.\
You can now start your docker setup. It will start building your custom image the first time you launch it, and will reuse the built one every other startup.
{% endhint %}

If you ever want to rebuild your image without deleting it before, just use this command:

```
docker-compose build
```

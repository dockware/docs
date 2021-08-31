# Start Developing

We start by preparing our environment.  
To develop new plugins, themes and features, in the most efficient way, we recommend a few things.

* **Docker Compose** This helps you to create a good architecture and blueprints of your setup. 
* **IDE with SFTP upload** We recommend [PhpStorm](https://www.jetbrains.com/de-de/phpstorm/) ❤️ It's just so good...and it also has automatic uploads. 
* **Chrome XDebug Extension** Dockware comes with plug'n'play XDebug. All you need is [this](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc?hl=en) extension to enable it in your browser.

\*\*\*\*

**1. Start Docker**

Alright, we're ready to start our development container.  
You need 2 basic things for this.

* docker-compose.yml \(template below\)
* a few commands plain or in makefile \(below\)

We recommend creating a new folder for your project that contains your docker files.

**Template File**

Use this template file to get started.  
It already comes with all ports you might need and volume mounting for both the database and the DocRoot of Shopware.

You can decide to either use the prepared "dev" image with a ready to use Shopware, or use the new "essentials" image that provides you a full environment without an installed Shopware. \(see instructions [below](https://dockware.io/docs#dockware-essentials)\)

**docker-compose.yml**

```yaml
version: "3"

services:

    shopware:
      image: dockware/dev:latest
      container_name: shopware
      ports:
         - "80:80"
         - "3306:3306"
         - "22:22"
         - "8888:8888"
         - "9999:9999"
         - "9998:9998"
      networks:
         - web
      environment:
         - XDEBUG_ENABLED=1

networks:
  web:
    external: false
```

Now open your terminal, navigate to the directory with your docker-compose.yml and run this command:

```bash
docker-compose up -d
```

**2. Prepare Development**

Great, you should now have a running Shopware 6 on your local machine.  
The prepared application environment is **DEV** and should already contain everything you need.

You can immediately access the database with the exposed port **3306** \(see [FAQ](../faq/sequel-pro.md) for more\).

Now it's time to download the current version of Shopware to your host.  
This is required to have code completion and IntelliSense right in your IDE, and thus we need it.

```bash
mkdir -p ./src
docker cp shopware:/var/www/html/. ./src
```

**3. Prepare IDE**

{% hint style="success" %}
Since january 2021 and the docker engine always getting better we recommend to use [bind mounting](../tips-and-tricks/how-to-use-bind-mounting.md) as it is much more comfortable and easier. Only if you have performance problems with it you can also use our old standard way ftp, this will always work.
{% endhint %}

**Only if you want to use sftp**, otherwise see our [bind-mouting](../tips-and-tricks/how-to-use-bind-mounting.md) docs.

Open the **src** folder with your preferred IDE and wait until finished loading.  
Then add a new SFTP connection to your container. We recommend Automatic-Upload if possible.  
If you need the SSH/SFTP credentials, please see the [FAQ](../use-dockware/default-credentials.md) section for details.

If you have PhpStorm, we also recommend enabling the **Symfony Plugin** for better code completion.

{% hint style="success" %}
Congratulations! That's it, you're done and ready to develop your Shopware projects!
{% endhint %}

**4. Sample Plugin**

To get you started as fast as possible, we've already prepared a **Dockware Sample Plugin** which is already installed and active.  
So you can start right from this plugin with your experiments in Shopware 6....or create your own plugin.

{% hint style="info" %}
The plugin comes with an active storefront subscriber, sample unit tests and more.  
Please review the **README.md** file within the plugin directory for further instructions.
{% endhint %}


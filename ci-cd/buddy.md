# Buddy

Buddy ([https://app.buddy.works](https://app.buddy.works/)) is a great CI/CD tool that can be used as SaaS or OnPremise version. This page gives you instructions and some ideas on how to use dockware images in Buddy  pipelines.

Please keep in mind, these might not always be 100% accurate samples, because some things like installing dependencies and so on, might work different in your project. In fact, some of these things are only related to projects, but you should get the idea.

{% hint style="info" %}
Are we missing something? Just let us know and we'd be happy to add it here!
{% endhint %}

## Building, Testing, ...

Dockware has been built to have everything on board that you need for a smooth Shopware developer experience.

You can of course use those images in your build pipeline for whatever task you need to do. This can range from simple dependency installation, on to building of storefront and administration as well as unit testing, execution of static analyzers and more. Everything you usually need should be already installed (of course no project dependent dependencies and tools haha).

To use dockware for your actions, just add a new **Custom Build** action, open the **Environment** tab and select the dockware image and version that you want to use.

![](../.gitbook/assets/Edit\_action\_·\_Deployment\_\_Plugin\_\_·\_Buddy\_Webinar\_\_Shopware\_6\_.png)

This will automatically start a new dockware container and run the commands your action in this container.&#x20;

### Entrypoint

When dockware images are launched in the "Command Runner" mode (so just to execute things), then we also execute our entrypoint script. This script makes sure to switch to your requested PHP version, start MySQL and more.

If you want to speed up things, and do not need anything special from what is being done there, you could also turn off this behaviour by simply checking the option "**Reset the default entrypoint set by the image**" in the Buddy environment setting of this action.

Please note that some features might not work if you turn this off.

![Reset the default dockware entrypoint](../.gitbook/assets/Screen\_Shot\_2022-05-23\_at\_10\_53\_00.png)



### Permissions

There is one thing to keep in mind!\
We try to be as strict as possible in dockware with our approach on simulating a real server where you probably dont have "root" access in lots of cases.

Unfortunately this interfers with Buddy when it comes to public keys.\
Buddy automatically mounts the "id\_rsa" into your container, so it can be used out of the box.\
That could lead to permission problems.

**The solution is easy.** You can just run the container in Buddy as "**root**". That's it.\
This user has the most permissions anyway, so there's no reason why anything might not work.

![](../.gitbook/assets/Edit\_action\_·\_Deployment\_\_Plugin\_\_·\_Buddy\_Webinar\_\_Shopware\_6\_-2.png)

{% hint style="success" %}
Aweseome, you should now be able to run almost anything that might be important for your Shopware shop pipeline or action!
{% endhint %}

## Cypress Tests

This is a perfect usage for dockware and its pre-installed Shopware version including demo data.\
Let's assume we have a plugin where we already have some Cypress tests that need a Shopware instance.

We have to decide where we want to run our tests against. These might be some options for you.

* Run tests against a **Shopware instance inside Buddy**\
  (we'll cover this below).\

* Run tests against a **Shopware instance on a server**\
  (Buddy would only run Cypress in an action. Your Cypress tests (aka the BASE\_URL) should then run against your server domain).

As already mentioned, we go with the first approach, to run the tests against a Shopware instance, that we launch directly **within Buddy** (on the fly).

This can be done in various ways.

The most native and familiar one (if we face your current local setup) might be the [**Linux Virtual Machine action**](https://buddy.works/blog/introducing-linux-vm) in Buddy. This allows you to launch a plain Linux (Docker or Amazon Machine Image) and do whatever you want to do with it.

A different approach might be to use [Additional Services](https://buddy.works/docs/pipelines/services/services-and-databases) next to your pipeline.&#x20;

But for today we keep it as straight forward and flexible as possible and go with the Linux VM.

Add a new action in Buddy and select the Linux VM.

![](../.gitbook/assets/Add\_a\_new\_action\_·\_Cypress\_in\_Buddy\_\_Plugin\_\_·\_Buddy\_Webinar\_\_Shopware\_6\_-2.png)

Once created, open the **Run** tab.

This gives you access to the simple text editor section, where you can add separate commands line per line.

![](../.gitbook/assets/Screen\_Shot\_2022-02-23\_at\_00\_01\_32.png)

The commands we add to this area will start by downloading the correct dockware version that we need. This can either be done statically, or through Buddy variables (or whatever you come up with).

Afterwards we start it with the `docker run` command and use the HTTP port 80 as well as our required PHP version. You can also use any other supported ENV variable in here.

Once started, we just give the container a bit **time to launch** MySQL and all its services.&#x20;

{% hint style="success" %}
Congratulations, your Docker infrastructure with dockware is done!
{% endhint %}

All we have to do now is to upload the files into our container, and intall / build our plugin as we would usually do.

Once completed, we can install Cypress and start to **run the tests against our domain** that we have just created for this pipeline. We use a separate Cypress image that has everything prepared. Please note that our host is already a Docker container, so you might get errors when trying to launch Cypress on your own (Error: spawn Xvfb ENOENT). So just use the [official Cypress images](https://hub.docker.com/r/cypress/included).

To give the Cypress container access to your dockware container, just reuse the network of your hosting system (Linux VM).

{% hint style="success" %}
That's it, your Cypress tests should now run!
{% endhint %}



Here is the full script that you can use as a template for your custom setup.

```bash
docker run --rm -p 80:80 --name shop -d dockware/play:6.4.8.1
sleep 30
docker logs shop

docker cp ./plugin/. shop:/var/www/html/custom/plugins/DockwareSamplePlugin
docker exec shop bash -c 'sudo chown www-data:www-data /var/www/html/custom/plugins -R'

docker exec shop bash -c "php bin/console plugin:refresh"
docker exec shop bash -c "php bin/console plugin:install DockwareSamplePlugin --activate"
docker exec shop bash -c "php bin/console cache:clear"

cd ./plugin/tests/Cypress 
docker run --network host -v $PWD:/e2e -w /e2e -e CYPRESS_BASE_URL=http://localhost cypress/included:4.10.0

```

{% hint style="warning" %}
If you get an error like "Passthrough is not supported, GL is swiftshader", then this is to Chrome inside the latest Cypress images. I think one need to change the image itself, or at least how Cypress is launched. I just used an older image which worked for me.
{% endhint %}

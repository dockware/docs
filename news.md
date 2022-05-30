# News

## Latest News

### 2022-05-19

We've been recently asked about multi-environment setups with dockware.\
And yes, it's absolutely possible. In the end, it's just Docker, right?

But we still wanted to help you and created a [new tutorial page](development/multi-environment-setups.md) along with 2 examples on our [Github repository](https://github.com/dockware/examples).

Learn how to use the **dockware/proxy** in combination with **2 (or more) Shopware shops** and **custom domains**. Even the watchers work ;)

We hope you like it.



### 2022-04-28

{% hint style="danger" %}
Upcoming Breaking Changes for Pipelines!
{% endhint %}

Hi

We are currently working a lot on providing new features and fixes for you.\
With the help of Uwe (Kellerkinder) we figured out, that dockware cannot be used in pipeline system of e.g. Gitlab as expected to be.

Some of you might use dockware containers to run things like Unit Tests, Static Analyzers and more. The original idea of dockware was to launch a full Shopware shop, but it can of course be used as command runner too.

Unfortunately, if you used to run a separate command like "composer install", etc., the entrypoint of dockware was not used. This means that the PHP switching didn't work, MySQL was not available and more. The reason for this is, because we accidentally used a **CMD** command instead of an **ENTRYPOINT** command in the Dockerfile. And e.g. Gitlab did just **overwrite the CMD** part, aka our custom entrypoint.

This will be switched in an upcoming release of dockware.\
\
We will introduce a new **ENV** variable **DOCKWARE\_CI** which ensures that the container is **NOT BLOCKING** as it would usually do. Instead, it will run the entrypoint script with PHP switching, MySQL startup and more, and then run your custom command. Afterwards it will just quit the container. Please note, there are reasons why we cannot use the "default" CI ENV variable that those systems usually use.

This means, if you do not add the new ENV variable to your scripts, the container will then be a blocking container, which will likely to **break your pipeline**.

Please see this examples

```bash
# NON-BLOCKING -> running custom commands
docker run --rm -e PHP_VERSION=8.0 -e DOCKWARE_CI=1 dockware/dev:6.4.10.1 bash -c 'composer install'

# BLOCKING -> launch shop for development or test (cypress, ...)
docker run --rm -e PHP_VERSION=8.0 -p 80:80 dockware/dev:6.4.10.1
```

We still need a bit for the changes, but please change it as soon as possible.\
It doesn't break anything, if the ENV is not yet existing in your current images.\


Thanks



### 2022-03-23

Hi there! \
It's been a while, but no worries, we have something hopefully amazing for all MAC users out there.

Every now and then, we are still reading about dockware as a SFTP based solution. This is not 100% correct. The reason for SFTP was always a fast and reliable solution that is working on every platform. And this is, and will be (for now) the best approach of containers with SFTP.

But still, it's not the only way to work with Docker (yes, not even dockware in that case).\
So we have tried to figure out, how can we use Docker with Shopware (dockware) in combination with **bind-mounting on MACs** and still have it fast enough for development.\
And one primary goal was to avoid any additional tools or sync options that need to be installed or configured in an additional step. And yes, **we have a solution**!

We use anonymous volumes in combination with known folders that cause troubles with the performance. You can read everything about it in our new [Bind-Mounting guide](tips-and-tricks/how-to-use-bind-mounting.md).

In addition to this, we have a small instruction page on how to [start a new Shopware project](tips-and-tricks/create-a-new-project.md) from scratch and prepare it for your development steps.

And yes, with all these new instructions, we might have to clean up the documentation and give it a new structure soon...but that's a different story :)&#x20;

Happy coding!



### 2021-12-10

Since the launch of dockware it has not only been used for development, but also in CI/CD pipelines. The pre-installed Shopware version including demo data makes dockware a perfect companion for Cypress tests in the cloud and other nice things you can do.

With this, we are launching a new [CI/CD section](broken-reference) in our documentation, where we collect examples and templates for the different platforms and how to use dockware for different uses cases.

The first page with [Github and Cypress](ci-cd/github.md) is already available.

{% hint style="info" %}
Do you use dockware in any platform that is not yet listed?\
Be part of the community and drop us a line in order to get your platform listed here as well! Thank you
{% endhint %}



### 2021-12-06

Now that we have rolled out guides on how to develop Apps for Shopware, there might be questions on how to create and view **log files across different systems**.

So we have improved our [Pimp my Log guides](features/pimp-my-log.md) for shop developers, plugin manufacturers as well as  app developers.

You can read the full guide [here](features/pimp-my-log.md).

### 2021-12-02

It's finally here! **Shopware App Development** with dockware and Docker!

We tried to do our best to guide you through this new experience.

Dockware has investigated the best possible ways to have an **easy and smooth development environment**. There are so many ways to deal with this, so you can use whatever way you like. Still we came up with an idea, that you might enjoy using. Read more about it in our [documentation](development/app-development.md).

And the best thing is, we've created a complete sample application that is available in our [Open Source Example Repository on Github](https://github.com/dockware/examples).

Even better - we've created a [YouTube video](https://www.youtube.com/watch?v=4vZ8V\_IIjck) where we explain everything and guide you through the basics and our full sample application.

It's finally time to create the best Shopware apps out there! Are you in?

### 2021-11-26

PHP 8.1 has finally been released yesterday, and we just can't wait to use the official release version. And so we are happy to announce the **official support of PHP 8.1** in all our dockware images, where PHP 8.x makes sense.

But the new **dockware 1.4.3** versions come with even more features:

* Easy "make switch-php" for live PHP version switching across all images.
* VIM (and also NANO) is now available in all images, even non-dev images.
* IMAP PHP extension now available in all images

We hope you like the new version and enjoy working with PHP 8.1



### 2021-10-27

We're happy to announce the release of the 2 latest Shopware 5 version v5.7.5 + v5.7.6 for both, dockware **play** and **dev**.

In addition to this, we have a new goodie for you (currently not yet available in all images).

But the **makefile** in _/var/www_ will soon include a command to easily switch the PHP version at runtime.

This is a perfect companion for pipelines such as with Gitlab, where you cannot provide environment variables when booting the container. _(Thanks to Marcus from Kellerkinder for your help with this)._

Here's a sneak peak

```bash
make switch-php version=8.0
make switch-php version=7.4
```

And it has been designed to be as robust as possible (hopefully). So switching to either the same, or even a non-existing PHP version should not screw up things.

If you want to read more about PHP switching, see [this page](features/switch-php-version.md)

Give us a bit, but then then we rollout every image again for you.



### 2021-10-15

We've updated all our images with our new building pipelines.

This means, that all of those images will now also be built automatically for **multiple architectures**, such as for the Apple M1.

{% hint style="info" %}
So starting with today, all dockware images should now also work on M1!
{% endhint %}

_(Please keep in mind, that some older versions failed to build because of a random problem or missing unit tests, which is a blocker for us, but this is only a handful)_

__

In addition to this, we're happy to rollout these new features with **version 1.4.2** of dockware:

* Added "jq" package for JSON processing in bash
* Add PHP 5.6 support to essentials and flex image
* Composer was accidentally always updated, even if no ENV variable has been provided_._ This won't happen now, so it should always work offline too.
* Fix problems with XDEBUG on PHP 5.6



### 2021-10-13

We are aware of a connection problem with [https://getcomposer.org/](https://getcomposer.org/) today.&#x20;

With that we found out that our images accidentally trigger a "composer update" in the entrypoint, even if no version has been defined as environment variable.

This should not happen. All dockware images must work offline without a problem.

While we are fixing the issue, please use a non-existing composer version as ENV variable. This will then trigger "nothing" in the entrypoint. (credits to ThLind)

```bash
docker run --env COMPOSER_VERSION=3 dockware/xyz....
```

Sorry for any inconvenience.



### 2021-09-14

We've released **dockware v1.4.1** for Shopware 6.4.4.1 and latest today.\
It comes with the following official fixes and will be rolled out across all images soon:

* Added new "make download" command to easily prepare the installation of Shopware in the "essentials" image. (already released)
* Fixed missing demo data in some Shopware 5 images (already released)
*   Fixed problem with wrong function definer with custom MySQL user ([https://github.com/dockware/dockware/issues/58](https://github.com/dockware/dockware/issues/58))\
    (Thanks to "_suc-sgor"_ for this)



### 2021-09-08

The latest release of **Shopware 5 (v5.7.3)** has been published for dockware PLAY and DEV today.\
Give it a test run with this command:

```bash
docker run --rm -p 80:80 dockware/play:5.7.3
```

Have fun with the new version!\


### 2021-09-06

We've added Shopware 6.4.4.0 for PLAY and DEV.

Allso, the Essentials image does now have a simple **makefile** that easily downloads your target Shopware 6 version for you. You can then simply continue your installation after choosing your version.

Check the command out [here](development/dockware-essentials.md#install-your-shopware-version).

### 2021-09-01

We're happy to announce that the new **dockware versions 1.4** are being rolled out.\
\
I think its the first mass rollout with more than 100 images being updated.\
we have a huge QA pipe by now (thats why some did not work and im glad about that) but if you face any problems, just let us know please.\
We did also go through the github issues and updated the labels. \
so you should now also know if something is broken because of shopware itself...which was sometimes confusing.\
\
how do you know what version you have? "docker logs" should show the version 1.4 at the top of the output when starting your container.\
\
Heres the changelog:

* new feature: ENV variable BUILD\_PLUGIN=myPluginName allows you to build your plugin with the provided dockware version without starting dockware directly.
* real demo data for Shopware > 6.2
* ready to use base.scss class in the dockware demo plugin (for watcher tests)
* PimpMyLog has now improved error logs for SW5 + SW6
* PimpMyLog is now PHP 8 ready
* SW5 has now Mailcatcher preconfigured...so it should automatically work
* different improvements and fixes across...yeah...everything

### 2021-03-12

Since docker engine is getting always better we now prefer to use [bind-mounting](tips-and-tricks/how-to-use-bind-mounting.md) instead of sftp.\
But sftp is still a good alternative if bind-mounting does not work for you well.

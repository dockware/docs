# News

## Latest News

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
* Composer was accidentally always updated, even if no ENV variable has been provided_. _This won't happen now, so it should always work offline too.
* Fix problems with XDEBUG on PHP 5.6



### 2021-10-13

We are aware of a connection problem with [https://getcomposer.org/](https://getcomposer.org) today. 

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

* new feature: ENV variable BUILD_PLUGIN=myPluginName allows you to build your plugin with the provided dockware version without starting dockware directly.
* real demo data for Shopware > 6.2
* ready to use base.scss class in the dockware demo plugin (for watcher tests)
* PimpMyLog has now improved error logs for SW5 + SW6
* PimpMyLog is now PHP 8 ready
* SW5 has now Mailcatcher preconfigured...so it should automatically work
* different improvements and fixes across...yeah...everything

### 2021-03-12

Since docker engine is getting always better we now prefer to use [bind-mounting](tips-and-tricks/how-to-use-bind-mounting.md) instead of sftp.\
But sftp is still a good alternative if bind-mounting does not work for you well.

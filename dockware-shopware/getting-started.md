# Getting Started

It's finally time to start your first Shopware 6 with dockware.\
For this we use the **dockware/shopware** image, which brings everything you need to simply start and explore Shopware 6.

{% hint style="warning" %}
Please keep in mind, that we only (for now) build Shopware versions starting from 6.7 with the new dockware/shopware image. For old versions please use the dockware/dev or dockware/play images. They will continue to work.
{% endhint %}

\
**Run Shopware 6 with latest version**

```bash
docker run --rm -p 80:80 dockware/shopware:latest
```

The dockware image will be downloaded the first time you run this command. Upcoming starts will not download it again, and will be much faster.

As soon as dockware is ready, you will see an output that shows you all available URLs.

That's it - give it a try!

```bash
** DOCKWARE IMAGE: dockware/shopware
** Tag: 6.7.2.1
** Built: Tue Jan  5 13:17:36 UTC 2021
** Copyright 2025 dasistweb GmbH
*******************************************************

launching dockware...please wait...

DOCKWARE: setting timezone to Europe/Berlin...

Current default time zone: 'Europe/Berlin'
Local time is now:      Fri Jan 29 07:39:40 CET 2021.
Universal Time is now:  Fri Jan 29 06:39:40 UTC 2021.

-----------------------------------------------------------
DOCKWARE: starting MySQL....
 * Starting MySQL database server mysqld
   ...done.
-----------------------------------------------------------
DOCKWARE: starting mailcatcher....
Starting MailCatcher
==> smtp://0.0.0.0:1025
==> http://0.0.0.0:1080/
*** MailCatcher runs as a daemon by default. Go to the web interface to quit.
-----------------------------------------------------------
DOCKWARE: starting cron service....
 * Starting periodic command scheduler cron
   ...done.
-----------------------------------------------------------
DOCKWARE: switching to PHP 8.4...
-----------------------------------------------------------
DOCKWARE: testing and starting Apache...
Syntax OK
 * Restarting Apache httpd web server apache2
   ...done.
-----------------------------------------------------------

WOHOOO, dockware/play:6.7.2.1 IS READY :) - let's get started
-----------------------------------------------------
DOCKWARE CHANGELOG: /var/www/CHANGELOG.md
PHP: PHP 8.4 (cli) (built: Nov 28 2020 06:24:43) ( NTS )
Apache DocRoot: /var/www/html/public
ADMINER URL: http://localhost/adminer.php
MAILCATCHER URL: http://localhost/mailcatcher
PIMPMYLOG URL: http://localhost/logs
SHOP URL: http://localhost
ADMIN URL: http://localhost/admin

What's new in this version? see the changelog for further details
https://www.shopware.com/de/changelog/

```

{% hint style="warning" %}
Attention! \
If you use a https connection and chrome as browser, it might block your connection to https://localhost. This can be easily changed by following [these steps](/broken/pages/-MSBsEoJvwJfXf3GtnGc) from our FAQ.
{% endhint %}

There are also other ways to start a Shopware 6 shop.

**Run Shopware 6 with specific version**

```bash
docker run --rm -p 80:80 dockware/shopware:6.7.2.1
```

**Run Shopware 6 with another PHP version**

```bash
docker run --rm -p 80:80 --env PHP_VERSION=8.3 dockware/shopware:latest
```

{% hint style="success" %}
If you are looking for passwords and default credentials of Shopware and dockware, please take a look at this page: [Default Credentials](default-credentials.md)
{% endhint %}

### Tutorial Video available

Watch our short video about starting and exploring  [Shopware 6 with dockware.](https://youtu.be/2gb8KHdGI6s)&#x20;

![Shopware 6 with dockware video](../.gitbook/assets/video-tutorial.jpg)

<br>

# Features

This image offers a variety of features that can be utilized both at startup and while it’s running. You can configure key settings when launching the container.&#x20;

Many features can also be adjusted dynamically during runtime.

## PHP Version

We've included multiple PHP versions in this image. So you can easily configure what you need in your specific project, without having to download lots of different images.

### Start with PHP version

If you want to start a container with a specific PHP version, just provide the **environment variable** either in the `docker run` command or within your **docker-compose.yml** file.

```bash
docker run -e PHP_VERSION=8.4 dockware/web:latest
```

```yaml
app:
   image: dockware/web:latest
   ...
   environment:
      - PHP_VERSION=8.4
```

{% hint style="danger" %}
Please keep in mind, if you start a container with an **invalid PHP version**, then it will crash.\
Just run it without a provided PHP version and navigate to **/etc/php** to see what is installed.\
Please see this page for more: [releases-and-versions.md](releases-and-versions.md "mention")
{% endhint %}

### Runtime Switch

You can also switch your PHP version at runtime, while the container is running.\
Navigate to **/var/www**, there we have prepared a **makefile** for you with various commands, such as `switch-php` that you can use in this case.

```bash
cd /var/www && make switch-php version=8.2
```

The command has been designed to be as **robust as possible**. This means, that nothing should break if you switch to the same PHP version, or even a version that is not supported and existing in the current image you use.

{% embed url="https://www.youtube.com/watch?v=LGukxv72Nm4" fullWidth="false" %}
Watch our video about switching while using "docker run"
{% endembed %}

{% embed url="https://www.youtube.com/watch?v=E8tmkLabSBc" %}
Watch our video about switching with your docker-compose.yml
{% endembed %}

## Node Version

We've included multiple Node versions in this image. So you can easily configure what you need in your specific project, without having to download lots of different images.

### Start with Node version

If you want to start a container with a specific Node version, just provide the **environment variable** either in the `docker run` command or within your **docker-compose.yml** file.

```bash
docker run -e NODE_VERSION=22 dockware/web:latest
```

```yaml
app:
   image: dockware/web:latest
   ...
   environment:
      - NODE_VERSION=22
```

{% hint style="danger" %}
Please keep in mind, if you start a container with an **invalid Node version**, then it will crash. \
Please see this page for more: [releases-and-versions.md](releases-and-versions.md "mention")
{% endhint %}

### Runtime Switch

You can also switch your Node version at runtime, while the container is running.\
Navigate to **/var/www**, there we have prepared a **makefile** for you with various commands, such as `switch-node` that you can use in this case.

```bash
cd /var/www && make switch-node version=22
```

## Supervisor

The image has a built-in Supervisor installed.

Supervisor is a **process control system** that allows you to manage and monitor long-running processes on Unix-based systems. It runs as a daemon, **ensuring** that specified programs **start**, **stop**, and **restart** as needed, making it useful for managing background services in production environments.&#x20;

You can easily use Supervisor by setting the ENV variable in your `docker run` command or in your **docker-compose.yml** file.

```bash
environment:
    - SUPERVISOR_ENABLED=1
```

Now you only need to mount a configuration file into the following location:

```
/etc/supervisor/supervisord.conf
```

And that's it, if you start your container, Supervisor is already started and ensures the commands provided in your configuration are correctly executed.

We recommend checking out the documentation of Supervisor, but here is a short sample configuration. It makes sure to always start 2 processes that consume your message queue.

```systemd
[program:my-worker-process]
process_name=%(program_name)s_%(process_num)02d
command=bin/console messenger:consume async
directory=/var/www/html
user=www-data
group=www-data
numprocs=2
autostart=true
autorestart=true
stdout_logfile=/var/log/worker.out.log
stderr_logfile=/var/log/worker.err.log
```

## Conjobs

The image includes built-in support for Cronjobs.

Cron is a **time-based job schedule**r in Unix-based systems that allows you to automate tasks by running scripts or commands at **scheduled intervals**. It is commonly used for repetitive tasks like backups, log rotation, or scheduled updates.

The cron service is **already started** in containers. So you just need to **add your scheduled tasks** to it and you're good to go.

You can of course do that manually (please see the cron documentation). But you can also automate setups. Here is a sample for a Shopware command - we create a file **my-project.txt**:

```bash
*/5 * * * * cd /var/www/html && php bin/console scheduled-task:run > /dev/null 2>&1
```

The command runs our scheduled tasks every 5 minutes.

Now we just need to **mount** or **copy** that file into the container and **add it to our cron service**.

```bash
docker exec -it container bash -c 'crontab /var/www/my-project.txt && sudo service cron restart'
```

That's it, all your tasks from my-project.txt are now automatically executed in the container.

## Filebeat

The image includes built-in support for Filebeat.

Filebeat is a **lightweight log shipper** that collects and forwards **log files** to Elasticsearch, Logstash, or other destinations. It is designed to run on servers, monitoring log files in real-time and efficiently transferring the data for analysis.

You can easily use Filebeat by setting the ENV variable in your `docker run` command or in your **docker-compose.yml** file.

```
environment:
    - FILEBEAT_ENABLED=1
```

Now you only need to mount a configuration file into the following location:

```
/etc/filebeat/filebeat.yml
```

And that's it, if you start your container, Filebeat is already started and collecting logs from the watched files according to your configuration.

We recommend checking out the documentation of Filebeat, but here is a short sample configuration. It watches log files in a **directory**, adds a **tag** "shop" to every line and sends the whole line to **logstash:5044**.

```bash
name: "shop"

filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/www/html/var/log/*.log
    tags: ["shop"]

output.logstash:
  hosts: ["logstash:5044"]
```

## Custom SSH/SFTP User

The image comes with a built-in SFTP service.&#x20;

You can easily create a custom user for SFTP/SSH access by setting the following ENV variables in your `docker run` command or in your **docker-compose.yml** file.

```
environment:
    - SSH_USER=shopware
    - SSH_PWD=mypwd
```

{% hint style="info" %}
If you don't need this, please refer to the default SSH credentials: [default-credentials.md](default-credentials.md "mention")
{% endhint %}

## Tideways

The image comes with Tideways installed.

Tideways is a **PHP performance monitoring and profiling tool** designed to help developers analyze and optimize application performance. It provides detailed insights into execution times, database queries, and bottlenecks, making it easier to debug slow requests and improve overall efficiency.

Once enabled, Tideways collects real-time performance data and can be integrated with Tideways.io for deeper analytics and monitoring in production environments.&#x20;

You can easily enable Tideways by setting the following ENV variable in your `docker run` command or in your **docker-compose.yml** file.

<pre><code><strong>environment:
</strong>    - TIDEWAYS_KEY=my-tideways-key
</code></pre>

By default, Tideways is configured with defaults for environment and service names. You can customize these using additional ENV variables:

Required:

* TIDEWAYS\_KEY - Your Tideways API key. Setting this activates Tideways. (Default: not-set)

Optional:

* TIDEWAYS\_ENVIRONMENT - Environment name for web (FPM) requests. (Default: production)
* TIDEWAYS\_SERVICE - Service name for web (FPM) requests. (Default: app)

## Xdebug

This image comes with an easy Xdebug solution.\
As you may know, sometimes Xdebug can be a bit tricky, so follow these simple to get it up and running.

### Step-by-Step

{% stepper %}
{% step %}
### Enable Xdebug

You can either enable Xdebug initially by setting the **ENV variable** `XDEBUG_ENABLED` to 1 (ON) or 0 (OFF) or by executing the **following command** at runtime:

```bash
# turn ON
cd /var/www && make xdebug-on
# turn OFF
cd /var/www && make xdebug-off
```
{% endstep %}

{% step %}
### Browser Extension

Install a browser extension, that sets the required headers in requests.\
Here is a Chrome Extension: [https://chromewebstore.google.com/detail/xdebug-chrome-extension/oiofkammbajfehgpleginfomeppgnglk?hl=de](https://chromewebstore.google.com/detail/xdebug-chrome-extension/oiofkammbajfehgpleginfomeppgnglk?hl=de)&#x20;

Install it and activate it in your browser.
{% endstep %}

{% step %}
### Listen in IDE

In most IDEs, you need to activate Xdebug or enable listening for Xdebug connections. Once activated, the next request from your browser extension should automatically trigger a breakpoint.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
That's it. This is how easy Xdebug can be
{% endhint %}

{% hint style="danger" %}
As Xdebug will slow down your dev environment for every request and also for each command like "cache:clear, watch-storefront" etc, you might not want to enable it all the time.
{% endhint %}

### Xdebug with API Clients

You might want to debug your API requests or other requets in clients without the Xdebug browser extensions.&#x20;

Simply append `XDEBUG_SESSION_START=PHPSTORM` as a GET parameter to your URL, and Xdebug will attach to the request. Most IDEs listen for incoming sessions regardless of the name, but if needed, adjust the value according to your IDE settings

### Advanced Configuration

We do also have additional environment variables, that you can use for further configuration. Please see our section about [environment-variables.md](environment-variables.md "mention")for more.

#### Remote Host

Both MAC and Windows have a Docker variable `host.docker.internal` available. This has been used by us as **default value** for the **Xdebug Remote Host.** Because that variable exists, this automatically uses the dynamic internal IP of your containers on MAC and Windows, and therefore it's indeed plug'n'play.

For **Linux** however this does not work! Please use **172.17.0.1** this as ENV variable to make it work! Here is a sample for a docker-compose.yml:

```bash
environment:
    - XDEBUG_REMOTE_HOST=172.17.0.1
```

If you use docker on **Windows with WSL2** you have to set your local ip address from the host in this ENV variable. Here is a sample for a docker-compose.yml:

```bash
environment:
    - XDEBUG_REMOTE_HOST=192.168.178.42
```

## Custom Timezone

You can adjust custom timezones for both the **operating system** as well as **PHP** by setting the following ENV variables. Dockware will automatically adjust settings and PHP configurations accordingly when you boot the image, or when you switch PHP versions.

```bash
environment:
    - TZ=Europe/Berlin
```

## Recovery Mode

If somehow anything goes wrong, and you need to acceess internal files of your existing containers, you can just start them with recovery mode enabled.

This will skip everything in the entrypoint, such as PHP and Node preparations, timezones and more.

```bash
environment:
    - RECOVERY_MODE=1
```

## Custom Apache DocRoot

The default Apache DocRoot is `/var/www/html`.

However, in some cases you might want to change this. You can easily do this by setting a specific configuration using the corresponding ENV variable.

```bash
environment:
    - APACHE_DOCROOT=/var/www/vhosts/my-website/html
```

## Running in CI/CD

If you run containers with `DOCKWARE_CI=1` the containers will automatically **quit** after running your command.&#x20;

{% hint style="warning" %}
Please note, our containers should usually automatically exit, once a custom command is provided. This is just a fallback if they do not exit as expected. So use this only if it doesn't work for any reason.
{% endhint %}

```bash
environment:
    - DOCKWARE_CI=1
```



## Health Checks

Our images provide a built-in health check since version ≥ 1.3.0 to make life easier for you.\
\
We provide plenty of options in the way how containers are started. Different PHP versions, extensions and way more. This means, sometimes it might take a few seconds longer, until your container is fully launched.

If your workflows (e.g. CI pipelines) rely on a fully launched container, this meant that you needed to add custom **sleep** or **wait** commands in the history. This was not perfect and sometimes a bit fragile.

We now support the **Docker Health Check** feature by providing a custom built-in check, that marks the container as healthy as soon as everything has been completed in the boot scripts.&#x20;

By using a simple **--wait** in your _docker-compose_ command, Docker will automatically wait until your container is healthy. This means, you don't need manual sleeps or other approaches anymore.

```bash
docker compose up -d --wait
```

If you use **docker run**, **--wait** is not supported, but you can use this simple line after starting the container, to get the same results (please adjust your container name).

```bash
until [ "$$(docker inspect -f '{{.State.Health.Status}}' my-container-name)" = "healthy" ]; do sleep 1; done
```

# Debugging

We love rock solid debugging tools and so do you!

XDebug is by far one of the most important debugging tools for PHP developers out there.\
That's why our dockware #dev images come with a plug'n'play solution out of the box.

All you need is the recommended Chrome Extension for XDebug.

Download the extension here:\
[https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc?hl=en](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc?hl=en)



## Configure Xdebug in Dockware

As soon as you start your dockware container with the environment variable **XDEBUG\_ENABLED=1**, you're ready to go.\
This environment variable helps you to turn ON or OFF XDebug in case you want to switch over to a production similar environment for some tests.

```yaml
shopware:
      image: dockware/dev:latest
      ...
      environment:
         - XDEBUG_ENABLED=1 // 1|0 for ON|OFF
```

## **Use Xdebug**

### **Use with Browser (Chrome)**

Enable Xdebug in your Chrome extension by clicking on the option "Debug".\
Now start your XDebug listener in your IDE and start debugging.

![enable Xdebug in Google Chrome](../.gitbook/assets/xdebug-enable-in-chrome.png)

### **Use with API clients**

You might want to debug your API requests or other requets in clients without the Xdebug Helper Tool. In this case you can simply append _`XDEBUG_SESSION_START=PHPSTORM`_ as a get parameter to your Url, and it will also debug this request.

**Toggling Xdebug**

{% hint style="danger" %}
As Xdebug will slow down your dev environment for every request and also for each command like "cache:clear, watch-storefront" etc. You might not want to enable it all the time. For this we have built another useful command.
{% endhint %}

In our global makefile in /var/www we provide the commands for instantly enabling and disabling Xdebug.

`cd /var/www && make xdebug-on`

`cd /var/www && make xdebug-off`

## **Advanced Configuration**

We do also have additional environment variables, that you can use for further configuration.\
Please see our section about [Environment Variables](../features/environment-variables.md) for more.

{% hint style="warning" %}
**XDEBUG and LINUX**\
Both MAC and Windows have the Docker variable "host.docker.internal" as default value, which should work great as Loopback IP to automatically recognize the host IP.

For Linux however this does not work! Please use **172.17.0.1** this as ENV variable to make it work!
{% endhint %}

{% hint style="warning" %}
**XDEBUG and WSL2**\
If you use docker on Windows with WSL2 you have to set your local ip address from the host in this env variable.

For example:&#x20;

DEBUG\_REMOTE\_HOST=192.168.178.42
{% endhint %}



## Troubleshooting

Here are a few things you can do, if its not yet working.

#### 1. Is XDebug activated?

See the startup output of your dockware container.\
It should show an output for PHP which should also display an activated XDebug.

```bash
docker logs (container_name)
```

#### 2. PHP Storm / IDE Configuration

Make sure that you have a correct mount configuration.\
This means that the root of your project should match the set root of your container.\
Also make sure that you have started the XDebug listener in PHPStorm for example.

#### 3. Chrome Extension

Do you have the correct chrome extension in the latest version installed?\
(see link above)

#### 4. Local PHP that blocks Port?

Do you have a local PHP installation on your host machine that might block the required port 9000?\
Make sure to kill this process. You can use the following snippet to find out if anything blocks your port.

```bash
lsof -i TCP:9000
```


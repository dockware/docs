# Switch PHP Version

Every dockware version allows you to switch to any PHP version that is supported by Shopware 6.\
This is done when starting your container.\
You can either use a command option when using "docker run", or set the PHP version in your docker-compose.yml file.\


**Set PHP Version with docker run**

```yaml
shopware:
      image: dockware/play:latest
      ...
      environment:
         - PHP_VERSION=7.2
```

Video Tutorial available

You can also watch a tutorial video. Let us know what you think and subscribe to our channel.

[dockware - switch php version with "docker run"](https://youtu.be/LGukxv72Nm4)

[dockware - switch php version with "docker-compose"](https://youtu.be/E8tmkLabSBc)

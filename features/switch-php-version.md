# Switch PHP Version

Every dockware version allows you to switch to any PHP version that is supported by Shopware 6.\
This is done when starting your container.\
You can either use a command option when using "docker run", or set the PHP version in your docker-compose.yml file.\


### **Switch with RUN command**

```bash
docker run --rm -p 80:80 -e PHP_VERSION=7.2 dockware/play:latest
```

{% embed url="https://www.youtube.com/watch?v=LGukxv72Nm4" %}
Watch our video about switching while using "docker run"
{% endembed %}

### **Switch with docker-compose**

```yaml
shopware:
      image: dockware/play:latest
      ...
      environment:
         - PHP_VERSION=7.2
```

{% embed url="https://www.youtube.com/watch?v=E8tmkLabSBc" %}
Watch our video about switching with your docker-compose.yml
{% endembed %}

### **Switch at Runtime**

```bash
make switch-php version=8.0
make switch-php version=7.4
```

The command has been designed to be as robust as possible.

This means, that nothing should break if you switch to the same PHP version, or even a version that is not supported and existing in the current image you use.


# Filebeat

The [Filebeat](https://www.elastic.co/beats/filebeat) integration allows you to automatically send log data and files to your [Logstash](https://www.elastic.co/logstash) instance if you use an [ELK](https://www.elastic.co/elastic-stack) stack.\
\
\


**How to configure filebeat?**

If you want to use Filebeat in dockware, you do not only need to turn ON the feature, but also provide a valid filebeat configuration.\
\
The easiest way to do this, is to mount that single file into **/etc/filebeat/filebeat.yml**.\
\
In addition to this, your Docker container must have access to your logstash container by either using the links or networks in Docker. This needs to be done only if you also have your Logstash within your Docker network :)\
\
Here is an example of a filebeat.yml configuration:

```yaml
name: "shop"

filebeat.inputs:
  - type: log
    enabled: true
    paths:
        - /var/log/apache2/error.log
    tags: ["server", "apache"]

  - type: log
    enabled: true
    paths:
        - /var/www/html/var/log/*.log
    tags: ["shop"]

output.logstash:
    hosts: ["logstash:5044"]
```

Please see our section about [Environment Variables](environment-variables.md) for more detail about the required configuration settings.\
\
\
Now that we have all single parts, we can combine them and create our configuration for our docker container.

```yaml
dockware:
      image: dockware/dev:latest
      ...
      volumes:
        - ./elk/my_shop/filebeat.yml:/etc/filebeat/filebeat.yml
      environment:
         - FILEBEAT_ENABLED=1
```

If you start your container, the "docker logs (container\_name)" output should show you that Filebeat is now being used.

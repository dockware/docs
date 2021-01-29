# Dockware and other images

 **Can i use dockware in combination with other images?**

Dockware is a Docker image like every other image. You can combine it with 3rd party services such as Redis, Elasticsearch, a separate MySQL, Percona or anything else.

Make sure they are on the same Docker network and use the key names of the containers as host addresses.

Here's an example:

{% code title="docker-compose.yml" %}
```yaml
version: "3"

services:
        
    shopware:
      image: dockware/play:latest
      container_name: shopware
      ports:
         - "80:80"
      networks:
         - web
      
    db:
      image: mysql:5.7
      container_name: mysql
      networks:
        - web
      environment:
        - MYSQL_ROOT_PASSWORD=hidden
        - MYSQL_USER=shopuser
        - MYSQL_PASSWORD=secret
        - MYSQL_DATABASE=shopware

networks:
  web:
    external: false
```
{% endcode %}

To access the standalone MySQL instance, use this connection string:

{% code title="IN DOCKER" %}
```yaml
mysql://shopuser:secret@db:3306/shopware
```
{% endcode %}


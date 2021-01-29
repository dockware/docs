# Persisting Data

We learned to avoid bind-mounting. But what if you want to persist data to have it all available again after recreating your containers?

Docker offers a way of using internal volumes to persist data. And that works fast, really fast.

So if you want to persist the state of your current work, use internal volume mountings and just do plain SFTP from your host.

Here's an example of a docker-compose.yml that persists the DocRoot of your shop and also your database:

{% code title="docker-compose.yml" %}
```yaml
version: "3"

services:
        
    shopware:
      image: dockware/play:latest
      container_name: shopware
      volumes:
         - "db_volume:/var/lib/mysql"
         - "shop_volume:/var/www/html"
      networks:
         - web
        
volumes:
  db_volume:
    driver: local
  shop_volume:
    driver: local

networks:
  web:
    external: false
```
{% endcode %}


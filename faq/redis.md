# Redis

## **How to use a Redis container?**

Redis is a Key/Value storage that allows you to save data outside your database with blazing fast access.\
Shopware supports the usage of Redis for session and cache handling.\
\
Here is a sample of a docker setup that adds a new Redis instance to your Docker network. Just add the container and make sure its on the same network.

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
      
    redis:
      image: redis:5.0.6
      container_name: redis
      networks:
        - web

networks:
  web:
    external: false
```
{% endcode %}

## **Shopware Configuration**

### **Connection**

Now make sure you configure the Redis instance to be used either for session handling, cache handling or both. 

Add the following to your ".env" file:

{% code title=".env (partial)" %}
```yaml
REDIS_SESSION_HOST=redis
REDIS_SESSION_PORT=6379
REDIS_CACHE_HOST=redis
REDIS_CACHE_PORT=6379
```
{% endcode %}

### Use Redis Caches

Please note, that you also need to use it for specific parts in Shopware.

See this page for more: [https://developer.shopware.com/docs/guides/hosting/performance/caches#example-replace-some-cache-with-redis](https://developer.shopware.com/docs/guides/hosting/performance/caches#example-replace-some-cache-with-redis)


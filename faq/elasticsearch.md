# Elasticsearch

**How to use Elasticsearch?**

Elasticsearch is a full-text, distributed NoSQL database for big data.  
It has amazing options for real-time searching and data analyzing.  
  
Shopware recommends the usage of Elasticsearch for large data sets of products.  
They also support in its search engine for better search results.  
  
Here is a sample of a docker setup that adds a new Elasticsearch instance to your Docker network. Just add the container and make sure its on the same network.

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
      
    elasticsearch:
      image: elasticsearch:7.5.2
      container_name: elasticsearch
      networks:
        - web
      environment:
        - "EA_JAVA_OPTS=-Xms512m -Xms512m"
        - discovery.type=single-node

networks:
  web:
    external: false
```
{% endcode %}

Now make sure you configure the Elasticsearch instance to be used.

Add the following to your ".env" file:

{% code title=".env \(partial\)" %}
```yaml
SHOPWARE_ES_HOSTS=elasticsearch:9200
SHOPWARE_ES_ENABLED=1
SHOPWARE_ES_INDEXING_ENABLED=1
SHOPWARE_ES_INDEX_PREFIX=abc
```
{% endcode %}

